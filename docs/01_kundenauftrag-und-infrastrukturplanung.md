# Kundenauftrag und Infrastrukturplanung

## Ausgangslage

Die virtuelle Infrastruktur wird auf einem Remote-Lab-Host mit 4 vCPU, 16 GB RAM und 500 GB Speicher aufgebaut. Die Planung berücksichtigt die vorhandenen Host- und VPN-Netze sowie eine Ressourcenreserve für den Host und spätere Erweiterungen.

## Startcheck

| Prüfpunkt | Status |
|---|---|
| Remote Lab erreichbar | OK |
| Hyper-V erreichbar | OK |
| Administrative Anmeldung möglich | OK |
| Installationsmedien vorhanden | OK |

## Systemplanung

| System | Hostname | vCPU | RAM | Datenträger | Reihenfolge |
|---|---|---:|---:|---|---:|
| Debian-Router | `RTR01` | 1 | 512 MB, Installation mit 1 GB | 20 GB | 1 |
| Domaincontroller | `DC01` | 2 | 2048 MB | 60 GB | 2 |
| Fileserver | `FS01` | 2 | 2048 MB | 60 GB + 80 GB Daten-VHDX | 3 |
| Windows-11-Client | `CL01` | 2 | 4096 MB | 80 GB | 4 |

## Adress- und VLAN-Plan

| System / Funktion | IP-Adresse | Gateway | VLAN |
|---|---|---|---:|
| Hyper-V-Host, Transit | `10.77.0.1/29` | – | untagged |
| `RTR01`, WAN | `10.77.0.2/29` | `10.77.0.1` | untagged |
| `RTR01`, Client-Gateway | `10.77.10.1/23` | – | 10 |
| `CL01` | `10.77.10.11/23` | `10.77.10.1` | 10 |
| `RTR01`, Server-Gateway | `10.77.20.1/24` | – | 20 |
| `DC01` | `10.77.20.10/24` | `10.77.20.1` | 20 |
| `FS01` | `10.77.20.20/24` | `10.77.20.1` | 20 |
| Erweiterungsnetz | `10.77.30.0/24` | `10.77.30.1` reserviert | 30 |
| Native/Parking-VLAN | kein produktives IP-Netz | – | 99 |

Das Clientnetz `/23` stellt 510 nutzbare Hostadressen bereit. VLAN 99 wird nicht produktiv verwendet.

## DNS und Domäne

| Einstellung | Wert |
|---|---|
| Domäne | `klemmbausteine.intern` |
| Interner DNS-Server | `DC01`, `10.77.20.10` |
| DNS während der Router-Installation | `10.100.0.1` |

## Installationsreihenfolge

1. `RTR01`
2. `DC01`
3. `FS01`
4. `CL01`

## Ergebnis

Hostnamen, Ressourcen, Datenträger, IP-Adressen, Gateways, VLANs und Installationsreihenfolge sind festgelegt. Für diesen Planungsauftrag sind keine Screenshots vorgesehen.
