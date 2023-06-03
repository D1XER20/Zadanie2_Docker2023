Plik Dockerfile definiuje budowę obrazu Docker dla aplikacji. Poniżej przedstawiam krótki opis poszczególnych instrukcji:

/# syntax=docker/dockerfile:1.2 - Wskazuje na użycie składni Dockerfile w wersji 1.2.

FROM node:14 as builder - Tworzy pierwszy etap budowy obrazu, oparty na obrazie Node.js w wersji 14. Ten etap będzie odpowiedzialny za zbudowanie aplikacji.

FROM node:alpine - Tworzy drugi etap budowy obrazu, oparty na lekkim obrazie Node.js Alpine. Ten etap będzie odpowiedzialny za uruchomienie aplikacji.

ENV NODE_OPTIONS=--openssl-legacy-provider - Ustawia zmienną środowiskową NODE_OPTIONS, która określa użycie starszego dostawcy OpenSSL.

ARG TRIVY_VERSION=0.18.3 - Definiuje argument TRIVY_VERSION, który pozwala na określenie wersji narzędzia Trivy.

WORKDIR '/app' - Ustawia katalog roboczy na '/app'.

COPY package.json . - Kopiuje plik package.json do katalogu roboczego.

RUN npm install - Wykonuje instalację zależności zdefiniowanych w pliku package.json.

COPY . . - Kopiuje resztę plików projektu do katalogu roboczego.

RUN npm run build - Wykonuje komendę 'npm run build' w celu zbudowania aplikacji.

FROM nginx - Tworzy trzeci etap budowy obrazu, oparty na obrazie Nginx. Ten etap będzie odpowiedzialny za uruchomienie serwera Nginx.

RUN wget https://github.com/aquasecurity/trivy/releases/latest/download/trivy_${TRIVY_VERSION}_Linux-64bit.tar.gz \ ... - Pobiera narzędzie Trivy z odpowiedniej wersji i umieszcza je w systemie.

RUN trivy --download-db-only - Pobiera bazę danych dla narzędzia Trivy, zawierającą informacje o znanych podatnościach.

EXPOSE 80 - Deklaruje, że aplikacja będzie nasłuchiwać na porcie 80.

COPY --from=0 /app/build /usr/share/nginx/html - Kopiuje zbudowane pliki aplikacji z pierwszego etapu (builder) do katalogu, który jest serwowany przez serwer Nginx.

Ten plik Dockerfile definiuje proces budowy obrazu, który składa się z trzech etapów. Pierwszy etap buduje aplikację, drugi etap instaluje narzędzie Trivy, a trzeci etap uruchamia serwer Nginx z zbudowaną aplikacją.
########################################################################################################################################
Plik .github/workflows/docker-build.yml zawiera łańcuch działań w Github Actions, który buduje obrazy Docker i przeprowadza testowanie pod kątem CVE. Poniżej przedstawiono krótki opis poszczególnych kroków:

name: Build Docker Images - Nazwa łańcucha działań, opisująca jego główne zadanie.

on: push - Określa, że łańcuch działań powinien być uruchamiany po pushu do repozytorium.

jobs: build - Definicja głównego zadania.

runs-on: ubuntu-latest - Określa, że zadanie ma być uruchamiane na systemie Ubuntu.

steps - Kolekcja kroków, które zostaną wykonane w ramach tego zadania.

Checkout repository - Pobiera zawartość repozytorium.

Set up QEMU - Konfiguruje QEMU, który jest używany do emulacji innych architektur sprzętowych.

Set up Docker Buildx - Konfiguruje Docker Buildx, który pozwala na budowanie wielu platformowych obrazów Docker.

Log in to GitHub Packages - Loguje się do GitHub Packages, aby umożliwić przesyłanie obrazów.

Build and push x86_64 image - Buduje obraz Docker dla architektury x86_64 i przesyła go do repozytorium GitHub Packages.

Test image for CVEs - Przeprowadza testowanie pod kątem CVE na obrazie x86_64 przy użyciu narzędzia Trivy.

Build and push arm64 image - Buduje obraz Docker dla architektury arm64 (M1/M2) i przesyła go do repozytorium GitHub Packages.

Get SemVer version - Wygenerowuje numer wersji w formacie SemVer na podstawie bieżącego daty i numeru uruchomienia.

Tag Docker image with SemVer - Dodaje tag SemVer do obrazów Docker na podstawie wygenerowanego numeru wersji.

Ten łańcuch działań zapewnia automatyzację procesu budowania obrazów Docker, testowania pod kątem CVE oraz tagowania obrazów zgodnie ze schematem SemVer.
########################################################################################################################################
