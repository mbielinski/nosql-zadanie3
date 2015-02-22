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
To zadanie robiłem w mongo w wersji 3.0

Najpierw podjąłem próbę konwersji XMLa na json'a za pomocą powershella ale 2 noce pracy komputera nie przynosiły efektów.
Na linuxie udało mi się za pomocą skryptu js wykonanego przez nodejs wykonać operację w około 20 minut. Robiłem też to za pomocą ściągniętogo konwertera z internetu w javie - sama operacja była szybsza jednak później miałem problem ze strukturą tego pliku.
Następnie przekonwertowałem plik Json na csv w Notepad++ bo miałem problem w imporcie do mongo pliku json.
```sh
Measure-Command  { .\mongoimport.exe --db wiki --collection wiki2 --type csv --file E:\temp3\wiki.csv --headerline }

Days              : 0
Hours             : 0
Minutes           : 0
Seconds           : 1
Milliseconds      : 647
Ticks             : 16473963
TotalDays         : 1,90670868055556E-05
TotalHours        : 0,000457610083333333
TotalMinutes      : 0,027456605
TotalSeconds      : 1,6473963
TotalMilliseconds : 1647,3963
```
Ilość rekordów - zaimportowałem plik z wikipedi okrojony "plwiki-latest-pages-articles1.xml"
```sh
db.wiki2.count()
106629
```
Skrypt mapreduce - najpierw podejmowałem próbę bez finalize przez co nie mogłem posortować bo do wartości trafiały również stringi.

```sh
db.wiki2Wystapienia.drop();
var map = function(){
  var words = this.text.toString().split(" ");
  words.forEach(function(key){
    emit(key, key);
  });
};
var reduce = function(key, value){
  return value.length;
};
var finalize = function(key, value){
    var notANumber = isNaN(value);
    return notANumber?1:parseInt(value);
}
result = db.wiki2.mapReduce( map, reduce, {out: 'wiki2Wystapienia',finalize: finalize});
```
db.wiki2Wystapienia.find().sort( { "value": -1 } ).limit(20);
```sh
/* 1 */
{
    "_id" : "infobox",
    "value" : 248
}

/* 2 */
{
    "_id" : "nazwa",
    "value" : 153
}

/* 3 */
{
    "_id" : "mi",
    "value" : 66
}

/* 4 */
{
    "_id" : "Nie",
    "value" : 64
}

/* 5 */
{
    "_id" : "on",
    "value" : 64
}

/* 6 */
{
    "_id" : "przed",
    "value" : 63
}

/* 7 */
{
    "_id" : "Fr",
    "value" : 61
}

/* 8 */
{
    "_id" : "postać",
    "value" : 61
}

/* 9 */
{
    "_id" : "Maria",
    "value" : 60
}

/* 10 */
{
    "_id" : "William",
    "value" : 60
}

/* 11 */
{
    "_id" : "nazwy",
    "value" : 60
}

/* 12 */
{
    "_id" : "ro",
    "value" : 60
}

/* 13 */
{
    "_id" : "h",
    "value" : 59
}

/* 14 */
{
    "_id" : "obr",
    "value" : 59
}

/* 15 */
{
    "_id" : "pa",
    "value" : 59
}

/* 16 */
{
    "_id" : "sz",
    "value" : 59
}

/* 17 */
{
    "_id" : "zjawisko",
    "value" : 59
}

/* 18 */
{
    "_id" : "IV",
    "value" : 58
}

/* 19 */
{
    "_id" : "Joseph",
    "value" : 58
}

/* 20 */
{
    "_id" : "Le",
    "value" : 58
}
```