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
cd /opt && sudo mkdir prometheus node_exporter
sudo mkdir /etc/prometheus
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
sudo mkdir /var/lib/prometheus
sudo cp prometheus-2.40.5.linux-amd64/prometheus.yml /etc/prometheus/
sudo cp ~/prometheus-2.40.5.linux-amd64/console* /etc/prometheus/
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
