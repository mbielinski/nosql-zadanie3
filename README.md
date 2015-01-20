#Wstęp 
Realizacje zadań prowadziłem na PC z wykorzystaniem maszyny wirtualnej windowsa 7.
PC host posiadał takie parametry:

Procesor: Intel Core i5 3470 4x3.20 GHz

RAM: DDR3 8GB 1600MHz Kingston HyperX XMP Beast (2x4GB, DualDDR, CL9)

Dysk Twardy: WD Red 1TB WD10EFRX (64MB, SATA/600)

System : Windows 7 Professional x64

#Zadanie 3

Najpierw wykonałem konwersję pliku txt do csv w powershell'u.
```sh
 import-csv word_list.txt -delimiter " " | export-csv wordlist.csv
```

Następnie zaimportowałem plik csv do mongodb.
```sh
	Measure-Command  {.\mongoimport.exe -db zad3 -c wordlist --file "C:\temp\wordlist.csv" -f "Word" --type csv}
	
Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 1
Milliseconds      : 69
Ticks             : 10692613
TotalDays         : 1,23757094907407E-05
TotalHours        : 0,000297017027777778
TotalMinutes      : 0,0178210216666667
TotalSeconds      : 1,0692613
TotalMilliseconds : 1069,2613
```