# Note : Baca setiap langkah dengan seksama, semua perintah dapat langsung disalin dan dijalankan pada server tanpa perlu copy satu per satu tiap perintah
## Unduh Prometheus dan Node Exporter versi terbaru
```bash
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.40.5/prometheus-2.40.5.linux-amd64.tar.gz && curl -LO https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz
```
## Ekstrak
```bash
tar -xzvf prometheus-2.40.5.linux-amd64.tar.gz && tar -xzvf node_exporter-1.5.0.linux-amd64.tar.gz
```
## Buat folder dan file yang dibutuhkan
```bash
cd /opt && sudo mkdir prometheus node_exporter && \
sudo mkdir /etc/prometheus && \
sudo cp ~/node_exporter-1.5.0.linux-amd64/node_exporter /opt/node_exporter/
```
## Buat service untuk node exporter
```bash
sudo nano /etc/systemd/system/node_exporter.service
```
## Salin semua teks berikut lalu simpan
```bash
[Unit]
Description=Node Exporter
Wants=network.target

[Service]
Type=simple
ExecStart=/opt/node_exporter/node_exporter \
 --web.listen-address=localhost:9100 --collector.systemd --collector.processes
[Install]
WantedBy=multi-user.target
Alias=node_exporter.service
```
## Selanjutnya mengatur service Prometheus dan file yang dibutuhkan
```bash
sudo mkdir /var/lib/prometheus && \
sudo cp ~/prometheus-2.40.5.linux-amd64/prometheus /opt/prometheus/ && \
sudo cp ~/prometheus-2.40.5.linux-amd64/console* /etc/prometheus/ && \
sudo nano /etc/systemd/system/prometheus.service
```
## Salin semua teks berikut lalu simpan
```bash
[Unit]
Description=Prometheus
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
ExecStart=/opt/prometheus/prometheus \
 --config.file=/etc/prometheus/prometheus.yml \
 --web.console.templates=/etc/prometheus/consoles \
 --web.console.libraries=/etc/prometheus/console_libraries \
 --web.page-title="Prometheus Console" \
 --storage.tsdb.path=/var/lib/prometheus/
[Install]
WantedBy=multi-user.target
Alias=prometheus.service
```
## Edit file promtheus.yml untuk menambahkan target metrics Node Exporter
```bash
sudo nano /etc/prometheus/prometheus.yml
```
## Salin teks berikut lalu simpan
```bash
global:
  scrape_interval: 5s # Set the scrape interval to every 15 seconds. Default is every 1 minute.
  evaluation_interval: 15s # Evaluate rules every 15 seconds. The default is every 1 minute.
  # scrape_timeout is set to the global default (10s).

# Alertmanager configuration
alerting:
  alertmanagers:
    - static_configs:
        - targets:
          # - alertmanager:9093

# Load rules once and periodically evaluate them according to the global 'evaluation_interval'.
rule_files:
  # - "first_rules.yml"
  # - "second_rules.yml"

scrape_configs:
  # Job untuk target Prometheus itu sendiri
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
   # Job untuk target Node Exporter
  - job_name: node
    static_configs:
      - targets: ["localhost:9100"]
```
## Setelah semua service dan file serta folder yang diperlukan telah dibuat, selanjutnya adalah menjalankan service Promtheus dan Node Exporter
```bash
sudo systemctl start prometheus && sudo systemctl start node_exporter
```
## Melihat status dari service 
### Untuk keluar dari status view klik tombol 'Q' pada keyboard
#### Berikut adalah perintah untuk melihat Service Prometheus
```bash
sudo systemctl status prometheus
```
#### Berikut adalah perintah untuk melihat Service Node Exporter
```bash
sudo systemctl status node_exporter
```

## Selanjutnya adalah menginstal Grafana
```bash
sudo apt-get install -y apt-transport-https && \
sudo apt-get install -y software-properties-common wget && \
curl -L https://apt.grafana.com/gpg.key | sudo apt-key add -
```
### Tambahkan repository grafana lalu instal
```bash
sudo apt-add-repository 'deb https://apt.grafana.com stable main' && \
sudo apt install grafana
```
