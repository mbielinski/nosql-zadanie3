#Wstęp 
Realizacje zadań prowadziłem na PC z wykorzystaniem maszyny wirtualnej windowsa 7.
PC host posiadał takie parametry:

Procesor: Intel Core i5 3470 4x3.20 GHz

RAM: DDR3 8GB 1600MHz Kingston HyperX XMP Beast (2x4GB, DualDDR, CL9)

Dysk Twardy: WD Red 1TB WD10EFRX (64MB, SATA/600)

System : Windows 7 Professional x64

#Zadanie 3 - część 1
Najpierw wykonałem konwersję pliku txt do csv w powershell'u.
```sh
 import-csv word_list.txt -delimiter " " | export-csv wordlist.csv
```

Następnie zaimportowałem plik csv do mongodb:
(wersja 2.6.5 standard)
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
(wersja 3.0) - widać zdecydowaną różnicę w wynikach (musiałem minimalnie zmienić komendę).
```sh
	 Measure-Command  { .\mongoimport.exe --db zad3 --collection wordlist2 --type csv --file "C:\temp\wordlist.csv" -f "Word"}
	
Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 0
Milliseconds      : 228
Ticks             : 2287205
TotalDays         : 2,64722800925926E-06
TotalHours        : 6,35334722222222E-05
TotalMinutes      : 0,00381200833333333
TotalSeconds      : 0,2287205
TotalMilliseconds : 228,7205
```


Skrypt mapReduce:
```sh
map = function() {  
  var alfabetycznie = Array.sum(this.Word.split("").sort().join(""));
  emit(alfabetycznie, this.Word );  
};
reduce = function(key, values) {  
  return values.toString();  
};
db.wordlist.mapReduce(map,  reduce,
  {
    out: "result"
  }
);

```
Skrypt wykonałem w robomongo:
```sh
{
    "result" : "result",
    "timeMillis" : 726,
    "counts" : {
        "input" : 8199,
        "emit" : 8199,
        "reduce" : 914,
        "output" : 7011
    },
    "ok" : 1,
```
W naszym pliku jest 914 anagramów.

Ilość elementów w wynikowej kolekcji:
```sh
db.result.count();
7011
```
Przykładowy element :
```sh
db.result.find().sort({_id:1}).limit(1);
{
    "_id" : "aaabcl",
    "value" : "cabala"
}
```
#Zadanie 3 - część 2
