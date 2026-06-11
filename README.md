# Przechowywanie i łamanie haseł

## Problem: jak bezpiecznie przechowywać hasła?

Najgorszym pomysłem jest zapisywanie haseł w bazie jako zwkyły tekst, np:
```
login: mkula
hasło: super_tajne_haslo
```

Jeżeli ktoś otrzyma dostęp do bazy danych, to od razu widzi wszystkie hasła. Mimo że jest to dość prosty błąd, to nawet duże firmy nie zawsze dbają o bezpieczeństwo danych. W 2024 roku firma Meta otrzymała 102 miliony dolarów kary za przechowywanie haseł do kont na Instagramie jako zwykły tekst jak w przykładzie, a dostęp do tej bazy miało ponad 20 000 pracowników Mety.

## Rozwiązanie: Hashowanie

- Czym jest hash?
- Hash to wynik działania tzw. funkcji skrótu np. md5sum:
```
Hasło: haslo
Hasło w postaci hashu: 207023ccb44feb4d7dadca005ce29a64
```

Funkcja hashująca zamienia dane wejściowe, np. hasło, na ciąg znaków o stałej długości. Taka funkcja działa tylko w jedną stronę, hasha nie da się "odszyfrować".

### Zadanie 1
Za pomocą komendy:
```bash
echo -n "haslo" | md5sum
```
Zahashuj hasła z przykładu:
- haslo123
- Haslo123
- haslo124
- haslo123!

Co daje wykorzystanie różnych znaków w haśle?

## Jak to działa w aplikacjach?
Przy rejestracji użytkownik podaje hasło. System nie zapisuje hasła, tylko oblicza hash i zapisuje go do bazy danych. Przy logowaniu użytkownik podaje hasło które za pomocą tej samej funkcji jest hashowane. Hash ten jest porównywany z tym zapisanym w bazie danych.

## Czy hash rozwiązuje problem?
Nie w pełni, jeżeli użytkownik ma słabe albo często używane hasło np.
- 123456
- qwerty123
- password

To atakujący może zgadywać hash takich haseł i porównywać z wynikami w bazie wykorzystując atak słownikowy. Dlatego ważne jest tworzenie unikalnych haseł. 

## Jak wygląda atak na hasło?
Atak słownikowy polega na sprawdzaniu popularnych haseł z listy. Jest to najprostsza, ale jedna z najbardziej efektywnych metod ataku. Jeżeli słyszymy o wycieku bazy danych lub haseł to możemy zakładać, że atakujący będzie próbował odgadnąć w ten sposób hasło. 

### Przykładowy atak na listę haseł

Załóżmy taką listę haseł:
```
123456
123456789
qwerty
password
1234567
```

Za pomocą programu `hashcat` możemy spróbować złamać hasła. W tym celu będziemy zapisywać hasła z listy do nowego pliku za pomocą komendy:

```bash
echo -n 'haslo' | sha256sum | cut -f1 -d' ' > sha256_hash_example
```
Następnie użyjemy programu `hashcat` i listy najpopularniejszych haseł:
```bash
hashcat -a 0 -m 1400 sha256_hash_example /usr/share/seclists/Passwords/Leaked-Databases/rockyou-75.txt
```

### Zadanie 2
Wykorzystując powyższe polecenia użyj hashcata do złamania dwóch haseł z listy. Ile czasu zajęło złamanie haseł?


### Mask attack
Atak typu maska jest używany do generowania słów pasujących do określenego wzorca. Ten atak jest używany gdy znamy długość hasła albo jego format, chociaż może być też wykorzystany do zwykłego ataku, jednak taki atak będzie czasochłonny, i nie ma pewności, że hasło uda się złamać. Maską będzie wzorzec składający się z liter małych i dużych, cyfr, i znaków specjalnych. Jest to metoda brute force - nie porównujemy haseł z jakąś listą jak w słowniku, tylko sprawdzamy wszystkie możliwe kombinacje.

### Zadanie 3
Rozważmy przypadek ataku na przykładowy kod blokady: `zaqwsx`. 
Zaszyfruj hasło do pliku `maska.txt`.
```bash
echo -n "zaqwsx" | sha256sum | cut -f1 -d' ' > maska.txt
```

Wykonamy dwa rodzaje ataku:
1. Wiemy, że hasło zawiera same litery

```bash
hashcat -a 3 -m 1400 maska.txt ?l?l?l?l?l?l 
```

Wyświetlenie hasha i hasła:
```bash
hashcat -m 1400 maska.txt --show
```

2. Nie wiemy co jest w haśle, ale wiemy, że zawiera sześć znaków.
```bash
hashcat -a 3 -m 1400 maska.txt ?a?a?a?a?a?a
```
```bash
hashcat -m 1400 maska.txt --show
```

### Zadanie 4
Zmodyfikuj hasło dodając myślnik na początek i wykonaj zmodyfikowaną komendę z punktu 2. Co się zmieniło?

### Zadanie 5
Jakiego problemu nie rozwiązuje wykorzystanie hashy?

### Sprawdzanie siły hasła
https://www.passwordmonster.com/
