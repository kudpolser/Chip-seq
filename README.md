# Chip-seq

#### Экстрагируйте риды и обрежьте их с помощью Trimmomatic
```
$ trimmomatic SE -phred33 ../../../shared/ChIP-seq_data/ChIP_ExuR.fastq ChIP_ExuR.fastq ILLUMINACLIP:TruSeq3-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
$ trimmomatic SE -phred33 ../../../shared/ChIP-seq_data/ChIP_UxuR_1.fastq ChIP_UxuR_1.fastq ILLUMINACLIP:TruSeq3-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
$ trimmomatic SE -phred33 ../../../shared/ChIP-seq_data/ChIP_UxuR_2.fastq ChIP_UxuR_2.fastq ILLUMINACLIP:TruSeq3-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
$ trimmomatic SE -phred33 ../../../shared/ChIP-seq_data/control_ExuR.fastq control_ExuR.fastq ILLUMINACLIP:TruSeq3-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
$ trimmomatic SE -phred33 ../../../shared/ChIP-seq_data/control_UxuR.fastq control_UxuR.fastq ILLUMINACLIP:TruSeq3-SE:2:30:10 LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 MINLEN:36
```
#### Наложите на геном E. coli K-12 MG1655 genome с помощью Bowtie

Скопируем геном E.coli на сервер:
```
scp -r -P 9022 E.coli_genome.fasta  polina_kudryavtseva@mg.uncb.iitp.ru:~/ChipSeq
```
Сгенерируем индексы по геному E.coli:
```
$ bowtie-build E.coli_genome.fasta e_coli
```

Выровняем риды на геном и сохраним в sam файл:
```
$ bowtie -S e_coli ChIP_ExuR.fastq ChIP_ExuR.sam
$ bowtie -S e_coli ChIP_UxuR_1.fastq ChIP_UxuR_1.sam
$ bowtie -S e_coli ChIP_UxuR_2.fastq ChIP_UxuR_2.sam
$ bowtie -S e_coli control_ExuR.fastq control_ExuR.sam
$ bowtie -S e_coli control_UxuR.fastq control_UxuR.sam
```
- ChIP_ExuR: reads that failed to align: 1159025 (5.37%)
- ChIP_UxuR_1: reads that failed to align: 20562418 (35.54%)
- ChIP_UxuR_2: reads that failed to align: 3636266 (15.30%)
- control_ExuR: reads that failed to align: 1850909 (9.64%)
- control_UxuR: reads that failed to align: 7587855 (33.17%)

```
$ samtools view -S -b ChIP_ExuR.sam > ChIP_ExuR.bam
$ samtools sort ChIP_ExuR.bam -o ChIP_ExuR.sorted.bam

$ samtools view -S -b ChIP_UxuR_1.sam > ChIP_UxuR_1.bam
$ samtools sort ChIP_UxuR_1.bam -o ChIP_UxuR_1.sorted.bam

$ samtools view -S -b ChIP_UxuR_2.sam > ChIP_UxuR_2.bam
$ samtools sort ChIP_UxuR_2.bam -o ChIP_UxuR_2.sorted.bam

$ samtools view -S -b control_ExuR.sam > control_ExuR.bam
$ samtools sort control_ExuR.bam -o control_ExuR.sorted.bam

$ samtools view -S -b control_UxuR.sam > control_UxuR.bam
$ samtools sort control_UxuR.bam -o control_UxuR.sorted.bam
```

#### Найдите пики с помощью MACS2 (подсказки тут - https://hbctraining.github.io/Intro-to-ChIPseq/lessons/05_peak_calling_macs.html) 
```
$ mkdir macs2
$ macs2 callpeak -t ChIP_ExuR.bam -c control_ExuR.bam -f BAM -g hs -n test -B -q 0.01
```
#### Гомологичны ли белки ExuR и UxuR? 
#### В каком эксперименте есть проблемы, с чем они могут быть связаны и как их решить? 
#### Предположите, чем могут отличаться эксперименты UxuR1 и UxuR2 с биологической точки зрения 
#### Используя ChIPMunk, найдите мотив для одного из этих белков (выберите белок, исходя из вашего ответа на п.6) 
#### (Optional) Поскольку вам рассказывали про МЕМE - сравните мотивы, полученные с помощью ChIPMunk и МЕМЕ. Какой мотив лучше и почему?

