# Sentiment Analysis application 

## Dockerfile
- frontend (`./frontend/Dockerfile`):
  - utilizzare l'immagine di base `nginx:1.15`
  - copiare i file nella cartella `build` all'interno `/usr/share/nginx/html`
  - il container ascolterà sulla porta 80 quindi se volete provare a lanciarlo ricordatevi di mappare una porta sulla porta 80
- webapp (`./webapp/Dockerfile`):
  - utilizzare l'immagine `openjdk:8-jdk-alpine` come base
  - ascolterà sulla porta 8080
  - copiare `target/sentiment-analysis-web-0.0.1-SNAPSHOT.jar` nella `/` del container
  - il container all'avvio deve eseguire `java -jar sentiment-analysis-web-0.0.1-SNAPSHOT.jar --sa.logic.api.url=${SA_LOGIC_API_URL}`
- logic (`./logic/Dockerfile`):
  - utilizzare l'immagine di base `python:3.6.6-alpine`
  - copiare il contenuto della cartella `sa` in `/app` del container
  - posizionarsi all'interno di `/app` nel container
  - eseguire `pip3 install -r requirements.txt`
  - eseguire `python3 -m textblob.download_corpora`
  - ascolterà sulla porta 5000
  - il container all'avvio deve eseguire `python3 sentiment_analysis.py`

- Buildate tutti i container con `docker build . ` ricordatevi anche di taggarli (`-t <nome-utente-docker-hub>/<nome-immagine>`)
- Testerete i vostri container scrivendo il docker-compose, quindi proseguite al prossimo step dopo aver buildato.

## docker-compose.yaml
- Scrivete un `docker-compose.yaml` per testare in locale la vostra applicazione, potete usare le immagini buildate e taggate precedentemente o specificare come buildarle direttamente nel docker-compose.
  - nella webapp settare la variabile d'ambiente `SA_LOGIC_API_URL` a `http://<dns_logic_tier>:5000` (5000 sarà la porta della nostra applicazione python)
- Esponete il frontend sulla porta 80 di localhost e la webapp sulla porta 32000 di localhost
  - Affichè il frontend possa comunicare con la webapp dovremo modificare `/etc/hosts` su linux o `c:\Windows\System32\Drivers\etc\hosts` su windows aggiungendo una riga `<ip-webapp-raggiungibile> app-sentiment`

## Kubernetes
- pushate le 3 immagini precedentemente create su docker-hub (ricordatevi di controllare che i tag abbiano la struttura corretta)
- create le risorse necessarie per deployare la vostra applicazione su kubernetes
- ricordate che webapp e frontend devono essere accessibili dall'esterno come services (NodePort, ma ricordatevi di specificare la porta per webapp a 32000)
  - modificate correttamente `/etc/hosts` o `c:\Windows\System32\Drivers\etc\hosts`
- controllate che funzioni tutto dal browser
