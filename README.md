# simple-app

Aplikasi web sederhana berbasis **Flask** yang di-containerize dengan **Docker** dan di-deploy lewat pipeline CI/CD **Jenkins** (Checkout dari GitHub → Build Docker Image → Push ke Docker Hub).

## Tech Stack

- [Flask](https://flask.palletsprojects.com/) 2.2.5
- Python 3.10 (slim image)
- Docker
- Jenkins (Declarative Pipeline)

## Struktur Proyek

```
simple-app/
├── app.py            # Aplikasi Flask utama
├── requirements.txt  # Dependency Python
├── Dockerfile         # Definisi image Docker
├── Jenkinsfile        # Pipeline CI/CD Jenkins
└── LICENSE
```

## Menjalankan Secara Lokal

```bash
pip install -r requirements.txt
python app.py
```

Aplikasi akan berjalan di `http://localhost:5000`.

## Menjalankan dengan Docker

Build image:

```bash
docker build -t simple-app .
```

Jalankan container:

```bash
docker run -p 5000:5000 simple-app
```

Akses di browser: [http://localhost:5000](http://localhost:5000)

## CI/CD Pipeline (Jenkins)

Pipeline di `Jenkinsfile` menjalankan tahapan berikut setiap kali build dijalankan:

1. **Checkout** — mengambil source code terbaru dari repository GitHub ini.
2. **Build** — langkah build awal (placeholder/logging).
3. **Build Docker Image** — build image Docker dan tag dengan nomor build Jenkins (`riset321/simple-app:<BUILD_NUMBER>`).
4. **Push Docker Image** — login ke Docker Hub menggunakan kredensial yang tersimpan di Jenkins (`dockerhub-credentials`), lalu push image dengan tag versi dan tag `latest` ke [Docker Hub](https://hub.docker.com/r/riset321/simple-app).

Image hasil build bisa langsung ditarik dari Docker Hub:

```bash
docker pull riset321/simple-app:latest
docker run -p 5000:5000 riset321/simple-app:latest
```

## Lisensi

Proyek ini menggunakan lisensi [MIT](LICENSE).
