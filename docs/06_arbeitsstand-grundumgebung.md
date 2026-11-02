# Arbeitsstand Grundumgebung

## Systeme

| System | Betriebssystem | Adresse | Funktion |
|---|---|---|---|
| `RTR01` | Debian 13 | `10.77.0.2/29` | Routing, VLANs und NAT |
| `DC01` | Windows Server 2025 | `10.77.20.10/24` | Domaincontroller und DNS |
| `FS01` | Windows Server 2025 | `10.77.20.20/24` | Fileserver |
| `CL01` | Windows 11 Pro | `10.77.10.11/23` | Domänenclient |

## Netzwerk

| Bereich | Netz | VLAN |
|---|---|---:|
| Transit | `10.77.0.0/29` | untagged |
| Clients | `10.77.10.0/23` | 10 |
| Server | `10.77.20.0/24` | 20 |
| Erweiterung | `10.77.30.0/24` | 30 |
| Native/Parking | kein produktives IP-Netz | 99 |

- `vSW-TRANSIT`: interner Hyper-V-Switch
- `vSW-LAB`: privater Hyper-V-Switch
- Windows-NAT: `NAT-LF11b`
- Routing zwischen Client- und Servernetz erfolgreich geprüft

## Domäne und DNS

- Domäne: `klemmbausteine.intern`
- NetBIOS-Name: `KLEMMBAUSTEINE`
- Domaincontroller und DNS: `DC01`
- DNS-Forwarder: `10.100.0.1`
- Reverse-Lookup-Zonen für Client-, Server- und Erweiterungsnetz eingerichtet
- `FS01` und `CL01` in die Domäne aufgenommen
- Secure Channel auf beiden Systemen erfolgreich geprüft

## mRemoteNG

mRemoteNG wurde auf dem Remote-Lab-Host als portable Anwendung eingerichtet.

Im Ordner `Klemmbausteine` wurden folgende Verbindungen angelegt und getestet:

| Ziel | Protokoll | Adresse |
|---|---|---|
| `RTR01` | SSH2 | `10.77.0.2:22` |
| `DC01` | RDP | `10.77.20.10:3389` |
| `FS01` | RDP | `10.77.20.20:3389` |
| `CL01` | RDP | `10.77.10.11:3389` |

Passwörter wurden nicht dokumentiert.

## Fileserver

- System-VHDX: 60 GB
- Daten-VHDX: 80 GB
- Daten-VHDX: RAW, nicht initialisiert und nicht formatiert

## Abschluss

- Kernprüfungen ohne offenen Fehler abgeschlossen
- `M01-Grundumgebung` für alle vier VMs erstellt
- Dokumentation unter `docs` abgelegt
- Nachweise unter `images` abgelegt
