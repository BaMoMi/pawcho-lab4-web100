# Sprawozdanie z laboratorium nr 4 - PAwChO

**Temat:** Analiza struktury obrazów Docker oraz wykorzystanie plików Dockerfile  
**Obraz:** `web100`  
**Student:** Wiktor Fura  
**E-mail:** s101560@pollub.edu.pl  
**Grupa dziekańska:** IO6  
**Repozytorium GitHub:** https://github.com/BaMoMi/pawcho-lab4-web100  
**Repozytorium DockerHub:** https://hub.docker.com/r/bamomi/web100  

## 1. Cel zadania

Celem zadania było przygotowanie pliku `Dockerfile`, a następnie zbudowanie na jego podstawie obrazu Docker o nazwie `web100`. Obraz miał bazować na najnowszym Ubuntu, zawierać zaktualizowany system, zainstalowany serwer HTTP Apache oraz skopiowaną stronę WWW z danymi studenta.

## 2. Etap 1 - przygotowanie projektu i Dockerfile

Utworzono katalog projektu oraz przygotowano pliki wymagane do zbudowania obrazu. Po wygenerowaniu początkowej struktury projektu dostosowano plik `Dockerfile` do wymagań ćwiczenia.

Struktura katalogu projektu:

```text
lab4_web100/
├── Dockerfile
├── index.html
├── .dockerignore
├── README.md
└── sprawozdanie_lab4.md
```

Zawartość pliku `index.html` obejmuje imię i nazwisko studenta, adres e-mail oraz grupę dziekańską. Strona jest kopiowana do katalogu `/var/www/html/index.html`, czyli standardowej lokalizacji strony startowej serwera Apache.

## 3. Ostateczna zawartość pliku Dockerfile

```dockerfile
FROM ubuntu:latest

LABEL org.opencontainers.image.authors="Wiktor Fura <s101560@pollub.edu.pl>"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get -y upgrade \
    && apt-get install -y --no-install-recommends apache2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

COPY index.html /var/www/html/index.html

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]
```

## 4. Etap 2 - modyfikacje i uzasadnienie

W pliku `Dockerfile` wprowadzono zmiany wynikające z dobrych praktyk tworzenia obrazów Docker:

1. Zastosowano `ubuntu:latest`, ponieważ zadanie wymagało użycia najnowszej wersji obrazu bazowego Ubuntu.
2. Dane autora zapisano w etykiecie `LABEL org.opencontainers.image.authors`, co pozwala przechowywać metadane obrazu.
3. Aktualizację systemu i instalację Apache połączono w jednej instrukcji `RUN`. Dzięki temu zmniejszono liczbę dodatkowych warstw obrazu.
4. Użyto opcji `--no-install-recommends`, aby nie instalować zbędnych pakietów rekomendowanych.
5. Po instalacji wykonano `apt-get clean` oraz usunięto `/var/lib/apt/lists/*`, aby ograniczyć rozmiar końcowego obrazu.
6. Do obrazu kopiowany jest wyłącznie plik `index.html`, co upraszcza kontekst budowania.
7. Zastosowano `EXPOSE 80`, aby informacyjnie wskazać port używany przez Apache.
8. Polecenie startowe zapisano w formie exec: `CMD ["apachectl", "-D", "FOREGROUND"]`, aby proces Apache działał jako główny proces kontenera.

## 5. Budowanie obrazu

Obraz zbudowano poleceniem:

```bash
docker image build --file Dockerfile --tag web100:latest .
```

Po zakończeniu budowania można sprawdzić obecność obrazu:

```bash
docker images | grep web100
```

## 6. Uruchomienie i test działania

Kontener testowy można uruchomić poleceniem:

```bash
docker run --name web100_test -d -p 8080:80 web100:latest
```

Następnie należy wejść w przeglądarce na adres:

```text
http://localhost:8080
```

albo sprawdzić odpowiedź z terminala:

```bash
curl http://localhost:8080
```

Po zakończeniu testu kontener można zatrzymać i usunąć:

```bash
docker stop web100_test
docker rm web100_test
```

## 7. Sprawdzenie liczby warstw

Liczbę warstw obrazu można sprawdzić poleceniem:

```bash
docker inspect web100:latest --format '{{len .RootFS.Layers}}'
```

Dodatkowo historię warstw można wyświetlić poleceniem:

```bash
docker image history web100:latest
```

Dla przygotowanego pliku `Dockerfile` obraz powinien posiadać zwykle 3 warstwy wynikające z instrukcji modyfikujących system plików: warstwę obrazu bazowego Ubuntu, warstwę z instrukcji `RUN` oraz warstwę z instrukcji `COPY`. Ostateczną liczbę warstw należy potwierdzić wynikiem komendy `docker inspect` w lokalnym środowisku.

## 8. Wysłanie obrazu do DockerHub

Po zbudowaniu obrazu należy nadać mu tag zgodny z repozytorium DockerHub i wysłać obraz:

```bash
docker login
docker tag web100:latest bamomi/web100:latest
docker push bamomi/web100:latest
```

Adres repozytorium DockerHub:

```text
https://hub.docker.com/r/bamomi/web100
```

## 9. Wnioski

W ramach ćwiczenia przygotowano obraz Docker z serwerem Apache. Wykorzystano obraz bazowy `ubuntu:latest`, zaktualizowano system, zainstalowano Apache oraz skopiowano stronę WWW zawierającą dane studenta. Plik `Dockerfile` został zoptymalizowany przez ograniczenie liczby instrukcji `RUN`, czyszczenie cache pakietów oraz zastosowanie pliku `.dockerignore`.
