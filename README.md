# PAwChO - Laboratorium 4

Obraz Docker `web100` z serwerem HTTP Apache. Obraz bazuje na `ubuntu:latest`.

Dane:

- Student: Wiktor Fura
- E-mail: s101560@pollub.edu.pl
- Grupa dziekańska: IO6

## Budowanie obrazu

```bash
docker build -t web100:latest .
```

## Uruchomienie kontenera

```bash
docker run -d --name web100-test -p 8080:80 web100:latest
```

Strona działa pod adresem:

```text
http://localhost:8080
```

## Liczba warstw

```bash
docker inspect web100:latest --format "{{len .RootFS.Layers}}"
```

Wynik:

```text
4
```

## DockerHub

```bash
docker tag web100:latest wiktorfff/web100:latest
docker push wiktorfff/web100:latest
```

Repozytorium DockerHub:

```text
https://hub.docker.com/r/wiktorfff/web100
```
