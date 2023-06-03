Dodano nowe kroki Get SemVer version i Tag Docker image with SemVer.
W kroku Build and push x86_64 image i Build and push arm64 image zmieniono tagi obrazów na ${{ github.run_number }}-x86_64 i ${{ github.run_number }}-arm64, które zawierają numer bieżącego uruchomienia jako część tagu. Dzięki temu każde uruchomienie łańcucha działań spowoduje wygenerowanie unikalnego tagu dla obrazów.
W kroku Tag Docker image with SemVer wykorzystano akcję docker/metadata-action@v3, która taguje obrazy Docker zgodnie ze schematem SemVer na podstawie wygenerowanego wcześniej numeru uruchomienia.
########################################################################################################################################
b. Aby udowodnić, że kolejne uruchomienie łańcucha działań spowoduje poprawne wygenerowanie numeru kolejnej wersji obrazu, możemy przeprowadzić następujące kroki:
Wykonać push do repozytorium GitHub na branchu main.
Przejść do zakładki "Actions" w repozytorium GitHub, aby zobaczyć uruchomienie łańcucha działań.
Po zakończeniu łańcucha działań, sprawdzić tagi obrazów Docker w repozytorium GitHub Packages. Powinny zawierać tagi w formacie SemVer, np. 2023.6.3-1-x86_64.
Uruchomić ponownie łańcuch działań przez kolejny push na branchu main.
Po zakończeniu łańcucha działań, sprawdzić tagi obrazów Docker w repozytorium GitHub Packages. Powinny zawierać nowe tagi z kolejnym numerem wersji, np. 2023.6.3-2-x86_64.
W ten sposób można potwierdzić, że kolejne uruchomienia łańcucha działań skutkują poprawnym wygenerowaniem numerów kolejnych wersji obrazów zgodnie ze schematem SemVer.