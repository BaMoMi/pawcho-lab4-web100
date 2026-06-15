# Sprawozdanie - PAwChO laboratorium 4

**Temat:** Analiza struktury obrazów Docker i wykorzystanie Dockerfile  
**Obraz:** `web100`  
**Student:** Wiktor Fura  
**E-mail:** s101560@pollub.edu.pl  
**Grupa dziekańska:** IO6  
**GitHub:** https://github.com/BaMoMi/pawcho-lab4-web100  
**DockerHub:** https://hub.docker.com/r/wiktorfff/web100  

## 1. Cel ćwiczenia

Celem ćwiczenia było przygotowanie pliku `Dockerfile` i zbudowanie obrazu Docker o nazwie `web100`. Obraz miał korzystać z najnowszego Ubuntu, mieć zaktualizowany system, zainstalowany serwer HTTP Apache oraz własną stronę WWW z danymi studenta.

## 2. Pliki projektu

W projekcie znajdują się następujące pliki:

```text
Dockerfile
index.html
.dockerignore
README.md
sprawozdanie_lab4.md
```

Plik `index.html` został skopiowany do katalogu `/var/www/html/index.html`, czyli domyślnego katalogu strony startowej Apache.

## 3. Dockerfile

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

## 4. Uzasadnienie zmian w Dockerfile

Do budowy wykorzystałem `ubuntu:latest`, ponieważ takie było wymaganie w zadaniu. Dane autora zostały zapisane w instrukcji `LABEL`. Aktualizacja systemu, instalacja Apache i czyszczenie cache zostały wykonane w jednej instrukcji `RUN`, żeby nie tworzyć niepotrzebnych dodatkowych warstw.

Użyłem też opcji `--no-install-recommends`, aby nie instalować pakietów, które nie są potrzebne do działania prostego serwera WWW. Po instalacji wykonałem `apt-get clean` i usunąłem katalog `/var/lib/apt/lists/*`, co zmniejsza rozmiar obrazu. Instrukcja `EXPOSE 80` informuje, że kontener korzysta z portu 80. Apache jest uruchamiany poleceniem `CMD ["apachectl", "-D", "FOREGROUND"]`, dzięki czemu działa jako główny proces kontenera.

## 5. Budowanie obrazu

Obraz został zbudowany poleceniem:

```bash
docker build -t web100 .
```

Budowanie zakończyło się poprawnie, a obraz został zapisany lokalnie jako `web100:latest`.

## 6. Uruchomienie i sprawdzenie działania

Kontener testowy został uruchomiony poleceniem:

```bash
docker run -d --name web100-test -p 8080:80 web100
```

Następnie sprawdziłem stronę w przeglądarce pod adresem:

```text
http://localhost:8080
```

Strona wyświetliła dane studenta, e-mail oraz grupę dziekańską.

## 7. Liczba warstw obrazu

Liczbę warstw sprawdziłem poleceniem:

```bash
docker inspect web100:latest --format "{{len .RootFS.Layers}}"
```

Wynik polecenia:

```text
4
```

Oznacza to, że przygotowany obraz posiada 4 warstwy systemu plików.

## 8. Wysłanie obrazu do DockerHub

Obraz został oznaczony tagiem zgodnym z nazwą repozytorium DockerHub:

```bash
docker tag web100:latest wiktorfff/web100:latest
```

Następnie obraz został wysłany poleceniem:

```bash
docker push wiktorfff/web100:latest
```

Repozytorium DockerHub:

```text
https://hub.docker.com/r/wiktorfff/web100
```

## 9. Wnioski

W ramach ćwiczenia został przygotowany działający obraz Docker z serwerem Apache. Po uruchomieniu kontenera strona była dostępna lokalnie na porcie 8080. Obraz został sprawdzony pod względem liczby warstw i wysłany do DockerHub.
