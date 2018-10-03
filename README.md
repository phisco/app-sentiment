# Sentiment Analysis application 

## Dockerfile
- frontend (`./frontend/Dockerfile`):
  - utilizzare l'immagine di base `nginx:1.15`
  - copiare i file nella cartella `build` all'interno `/usr/share/nginx/html`
  - il container ascolterà sulla porta 80 quindi se volete provare a lanciarlo ricordatevi di mappare una porta sulla porta 80
- webapp (`./webapp/Dockerfile`):
  - utilizzare l'immagine `openjdk:8-jdk-alpine` come base
  - settare la variabile d'ambiente `SA_LOGIC_API_URL` a `http://<dns del container con l'applicazione in python o ip>:5000` (5000 sarà la porta della nostra applicazione python, localhost ne)
  - esporre la porta 8080
  - copiare `target/sentiment-analysis-web-0.0.1-SNAPSHOT.jar` nella `/` del container
  - il container all'avvio deve eseguire `java -jar sentiment-analysis-web-0.0.1-SNAPSHOT.jar --sa.logic.api.url=${SA_LOGIC_API_URL}`
- logic (`./logic/Dockerfile`):
  - utilizzare l'immagine di base `python:3.6.6-alpine`
  - copiare il contenuto della cartella `sa` in `/app` del container
  - posizionarsi all'interno di `/app` nel container
  - eseguire `pip3 install -r requirements.txt`
  - eseguire `python3 -m textblob.download_corpora`
  - esporre la porta 5000
  - il container all'avvio deve eseguire `python3 sentiment_analysis.py`

- Buildate tutti i container con `docker build . ` ricordatevi anche di taggarli (`-t <nome-utente-docker-hub>/<nome-immagine>`)
- Testate i container mappando tutte le porte necessarie (80, 8080, 5000) su localhost e andando a [localhost:80](localhost:80) sul vostro browser
- L'applicazione in python sarà raggiungibile dagli altri container solo specificando l'ip preciso del containerr, ma possiamo testarla dalla nostra macchina con `curl --header "Content-Type: application/json" --request POST --data '{"sentence":"ciao"}' http://localhost:5000/analyse/sentiment`

## docker-compose.yaml
Scrivete un `docker-compose.yaml` per testare in locale la vostra applicazione, potete usare le immagini buildate e taggate precedentemente o buildatele di nuovo.
Attenzione alla variabile d'ambiente nella webapp (docker-compose ci permette di usare il dns)

## Kubernetes
- pushate le 3 immagini precedentemente create su docker-hub (ricordatevi di controllare che i tag abbiano la struttura corretta)
- 