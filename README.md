## Tree
```
├── docker-compose.yml
├── producer/
│   ├── producer_suhu.py
│   └── producer_kelembaban.py
└── consumer/
    └── pyspark_consumer.py
```

Sistem ini memantau kondisi suhu dan kelembaban gudang secara real-time menggunakan:
- Kafka untuk komunikasi data streaming
- PySpark untuk analisis real-time
- Docker Compose untuk menjalankan Zookeeper, Kafka, Producer, dan Spark secara berurutan dan saling terhubung.

File docker-compose.yml digunakan untuk menjalankan warehouse system monitoring berbasis Kafka dan Spark secara terintegrasi. Di dalamnya terdapat empat layanan utama:
- Zookeeper sebagai koordinator Kafka
- Kafka sebagai message broker untuk menampung data sensor
- Python Producer untuk mensimulasikan pengiriman data suhu dan kelembaban
- Spark (dengan PySpark) sebagai konsumer yang memproses data secara real-time menggunakan Spark Streaming.

Langkah-langkah Implementasi
1.  Setup Kafka dan Zookeeper
    ```jsx
    docker-compose up -d
    ```
    Kafka berjalan di `localhost:9092`, dan Zookeeper di `localhost:2181`.
    <details>
      <summary>Dokumentasi Step 1</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/faaa8c3d-dc7a-4322-8515-b123bf31478c">
    </details>
  
2. Buat Topik Kafka
    ```jsx
    docker exec -it kafka bash
    ```
    ```jsx
    kafka-topics --create --topic sensor-suhu-gudang --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
    kafka-topics --create --topic sensor-kelembaban-gudang --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
    ```
    <details>
      <summary>Dokumentasi Step 2</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/6834de1e-9d46-409d-b413-bd6b9f3abf69">
    </details>
  
3. Simulasikan Data Sensor (Producer Kafka)
   Jalankan dua skrip Python di `container producer`
   
   Command 1
    ```jsx
    docker exec -it producer bash
    ```
    Command 2 (Opsional)
    ```jsx
    pip install kafka-python
    ```
    Command 3
    ```jsx
    python producer_suhu.py
    ```
    <details>
      <summary>Dokumentasi producer_suhu.py</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/9044eb5e-12b4-40af-9794-a3483cc4d02e">
    </details>

    Command 4
    ```jsx
    python producer_kelembaban.py
    ```
    <details>
      <summary>Dokumentasi producer_kelembaban.py</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/5993b0ed-5789-4656-ab1a-0af7ed1eca5f">
    </details>

5. Konsumsi dan Olah Data dengan PySpark
    Jalankan skrip pyspark_consumer.py di `container spark`

    Command 1
    ```jsx
    docker exec -it spark bash
    ```
    Command 2 (Opsional)
    ```jsx
    pip install kafka-python
    ```
    Command 3
    ```jsx
    spark-submit --packages org.apache.spark:spark-sql-kafka-0-10_2.12:3.5.0 pyspark_consumer.py
    ```
    <details>
      <summary>Dokumentasi Peringatan Kritis</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/7e4e32c9-2ea6-465f-8f42-52c9d37b7f6c">
    </details>
    <details>
      <summary>Dokumentasi Peringatan Suhu Tinggi</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/fe74f586-62d7-4dfd-a7d9-0303420b81d3">
    </details>
    <details>
      <summary>Dokumentasi Peringatan Kelembaban Tinggi</summary>
      <img width="1500" alt="topologi" src="https://github.com/user-attachments/assets/79109c1a-e075-4486-9f3c-06581cab00c8">
    </details>


Sistem ini memungkinkan perusahaan untuk mendeteksi secara langsung apabila sebuah gudang mengalami suhu atau kelembaban yang tinggi, maupun keduanya secara bersamaan. Kemampuan ini sangat krusial untuk menjaga mutu penyimpanan barang-barang sensitif serta mencegah potensi kerugian dalam logistik.
