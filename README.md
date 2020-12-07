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

#### Найдите пики с помощью MACS2 (подсказки тут - https://hbctraining.github.io/Intro-to-ChIPseq/lessons/05_peak_calling_macs.html) 
```
$ mkdir macs2
$ macs2 callpeak -t ChIP_ExuR.sam -c control_ExuR.sam -g 4641652 -n exur -q 0.01 --nomodel --shiftsize 100
$ macs2 callpeak -t ChIP_UxuR_1.sam -c control_UxuR.sam -g 4641652 -n uxur1 -q 0.01 --nomodel --shiftsize 100
$ macs2 callpeak -t ChIP_UxuR_2.sam -c control_UxuR.sam -g 4641652 -n uxur2 -q 0.01 --nomodel --shiftsize 100
```
#### Гомологичны ли белки ExuR и UxuR? 
#### В каком эксперименте есть проблемы, с чем они могут быть связаны и как их решить? 
#### Предположите, чем могут отличаться эксперименты UxuR1 и UxuR2 с биологической точки зрения 
#### Используя ChIPMunk, найдите мотив для одного из этих белков (выберите белок, исходя из вашего ответа на п.6) 
#### (Optional) Поскольку вам рассказывали про МЕМE - сравните мотивы, полученные с помощью ChIPMunk и МЕМЕ. Какой мотив лучше и почему?

