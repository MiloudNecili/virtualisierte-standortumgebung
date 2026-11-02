# Projektübersicht

## Ziel

Aufbau einer repräsentativen, virtualisierten Standortumgebung mit getrennten Client- und Servernetzen, zentraler Benutzerverwaltung, DNS, Dateiablage und kontrollierter Außenanbindung.

## Systeme

| System | Rolle |
|---|---|
| `RTR01` | Debian-Router, VLAN-Routing und NAT |
| `DC01` | Domaincontroller und DNS |
| `FS01` | Fileserver mit separatem Datenvolume |
| `CL01` | Windows-11-Client |

## Netzwerkplanung

| Bereich | Netz | VLAN |
|---|---|---:|
| Transit | `10.77.0.0/29` | untagged |
| Clients | `10.77.10.0/23` | 10 |
| Server | `10.77.20.0/24` | 20 |
| Erweiterung | `10.77.30.0/24` | 30 |
| Native/Parking | kein produktives IP-Netz | 99 |

## Domäne

```text
klemmbausteine.intern
```

## Ablage

```text
docs/    Markdown-Dokumentation
images/  technische Nachweise aus der Laborumgebung
```

Für praktische Arbeitsaufträge werden zwei relevante Screenshots verwendet, bei Bedarf höchstens drei.
