# Technisches Abschlussaudit und Übergabevorbereitung

## Ziel

Der umgesetzte Infrastrukturstand wurde anhand von fünf klar abgegrenzten Stichproben geprüft. Das Audit konzentrierte sich auf Identitäten und Berechtigungen, Systemhärtung, Netzwerksegmentierung, den zentralen Administrationsweg sowie die Wiederherstellbarkeit einer sicherheitsrelevanten Konfiguration. Eine vollständige Neuprüfung der gesamten Umgebung wurde nicht durchgeführt.

## Auditumfang

| Nr. | Bereich | Soll-Zustand | Prüfmethode | Erwarteter Nachweis |
|---:|---|---|---|---|
| 1 | Identitäten und Berechtigungen | Die LAPS-Richtlinie wird auf `CL01` angewendet. | `gpresult /R /SCOPE COMPUTER` | `GPO-CLIENT-LAPS` unter den angewendeten Gruppenrichtlinienobjekten |
| 2 | Systemhärtung | Direkte SSH-Anmeldung für `root` ist auf `RTR01` deaktiviert. | Effektive SSH-Konfiguration mit `sshd -T` prüfen | `permitrootlogin no` |
| 3 | Netzwerksegmentierung | Direkte administrative Zugriffe aus dem Clientnetz sind blockiert. | Von `CL01` SSH zu `RTR01` und RDP zu `FS01` testen | Beide Verbindungen nicht erreichbar |
| 4 | Administrationsweg | Administrative Zugriffe aus dem Managementnetz funktionieren. | Von `ADM01` SSH zu `RTR01` und RDP zu `FS01` testen | Beide Verbindungen erreichbar |
| 5 | Betrieb und Wiederherstellung | Original, Sicherung und wiederhergestellte `nftables`-Konfiguration sind identisch. | SHA-256-Prüfsummen vergleichen | Drei identische Prüfsummen |

## Ergebnisse der Stichproben

### 1. LAPS-Richtlinie auf CL01

Die Computerrichtlinien auf `CL01` wurden mit `gpresult` geprüft. `GPO-CLIENT-LAPS` wurde unter den angewendeten Gruppenrichtlinienobjekten aufgeführt. Die Richtlinie wurde zuletzt von `DC01.klemmbausteine.intern` verarbeitet.

```text
GPO-CLIENT-BASIS
GPO-FIREWALL-ICMP
GPO-CLIENT-LAPS
GPO-DOMAIN-KONTENRICHTLINIE
Default Domain Policy
```

**Bewertung:** Erfüllt.

### 2. SSH-Härtung auf RTR01

Die effektive SSH-Konfiguration wurde direkt über `sshd -T` geprüft.

```text
permitrootlogin no
passwordauthentication yes
```

Die direkte SSH-Anmeldung für `root` ist deaktiviert. Die kennwortbasierte Anmeldung bleibt für den vorgesehenen Administrationsaccount `labadmin` verfügbar.

**Bewertung:** Erfüllt.

### 3. Direkte Administration aus dem Clientnetz

Von `CL01` wurden die vorgesehenen administrativen Zielverbindungen geprüft.

```text
CL01 -> RTR01 / SSH TCP 22   = False
CL01 -> FS01  / RDP TCP 3389 = False
```

Die direkte Verwaltung aus dem Clientnetz ist weiterhin blockiert.

**Bewertung:** Erfüllt.

### 4. Administration aus dem Managementnetz

Von `ADM01` wurden die gleichen administrativen Zielverbindungen geprüft.

```text
ADM01 -> RTR01 / SSH TCP 22   = True
ADM01 -> FS01  / RDP TCP 3389 = True
```

Der vorgesehene zentrale Administrationsweg über das Managementnetz ist verfügbar.

**Bewertung:** Erfüllt.

### 5. Wiederherstellbarkeit der nftables-Konfiguration

Die SHA-256-Prüfsummen der produktiven Konfiguration, der Sicherung und der wiederhergestellten Testkopie wurden verglichen.

```text
72c0f19a989e54511f4d221459608505795f5d56b1756f87f1ff85495def9df5  /etc/nftables.conf
72c0f19a989e54511f4d221459608505795f5d56b1756f87f1ff85495def9df5  /root/restore-backup/nftables.conf
72c0f19a989e54511f4d221459608505795f5d56b1756f87f1ff85495def9df5  /tmp/nftables-restored.conf
```

Die drei Dateien sind inhaltlich identisch.

**Bewertung:** Erfüllt.

## Auditbewertung

| Stichprobe | Ergebnis | Abweichung |
|---|---|---|
| LAPS-Richtlinie | Erfüllt | Keine |
| SSH-Härtung | Erfüllt | Keine |
| Clientnetz-Blockierung | Erfüllt | Keine |
| Zentraler Administrationsweg | Erfüllt | Keine |
| Konfigurations-Restore | Erfüllt | Keine |

In den fünf festgelegten Stichproben wurde keine tatsächliche Soll-Ist-Abweichung festgestellt. Daher wurden keine künstlichen Befunde erzeugt und keine technischen Korrekturen durchgeführt.

```text
Umgesetzte Korrekturen: 0
Erforderliche Nachtests: 0
```

## Kernmaßnahmen für die Abschlussdarstellung

| Bereich | Risiko und Maßnahme | Tatsächliche Wirkung | Technischer Nachweis |
|---|---|---|---|
| Systemhärtung | Direkten SSH-Root-Zugang deaktivieren | `root` kann nicht direkt über SSH verwendet werden | `permitrootlogin no` |
| Netzwerk und Administration | Clientnetz von administrativen Zugängen trennen; Administration über `ADM01` | Administrative Verbindungen aus `CL01` sind blockiert und aus `ADM01` erlaubt | Vergleich der SSH- und RDP-Verbindungstests |
| Betrieb und Wiederherstellung | Firewall-Konfiguration sichern und Restore verifizieren | Original, Backup und wiederhergestellte Kopie sind identisch | Identische SHA-256-Prüfsummen |

## Vorher-/Nachher-Vergleiche

### Administrationsweg

**Vorher:** Administrative Zielports waren aus dem Clientnetz erreichbar.

**Nachher:** Administrative Zugriffe erfolgen zentral über `ADM01` im Managementnetz. Der direkte Zugriff aus `CL01` ist blockiert.

### SSH-Härtung

**Vorher:** Der direkte Root-Zugang war nicht als wirksam geprüfte Schutzmaßnahme belegt.

**Nachher:** Die effektive Konfiguration weist `PermitRootLogin no` aus; die Administration erfolgt über `labadmin`.

### Wiederherstellbarkeit

**Vorher:** Die Wiederherstellbarkeit der Firewall-Konfiguration war nicht durch einen Inhaltsvergleich bestätigt.

**Nachher:** Original, Sicherung und wiederhergestellte Kopie wurden anhand identischer SHA-256-Prüfsummen verifiziert.

## Technische Nachweise für die Abschlussdarstellung

Für die Abschlussdarstellung werden folgende drei Nachweise verwendet:

1. Vergleich der administrativen Erreichbarkeit aus `CL01` und `ADM01`.
2. Effektive SSH-Konfiguration auf `RTR01` mit `permitrootlogin no`.
3. SHA-256-Vergleich der produktiven, gesicherten und wiederhergestellten `nftables`-Konfiguration.

## Management-Zusammenfassung

1. Administrative Zugriffe sind vom Clientnetz getrennt und über einen zentralen Administrationsweg im Managementnetz abgesichert.
2. Die geprüften Härtungsmaßnahmen für Windows-Systeme und den Debian-Router sind wirksam und technisch nachvollziehbar.
3. Zentrale Wiederherstellungsverfahren wurden praktisch geprüft; die Firewall-Konfiguration konnte ohne Abweichung wiederhergestellt werden.

## Sicherung des Abschlussstands

Der geprüfte Zustand wurde für alle fünf virtuellen Systeme als Hyper-V-Checkpoint gespeichert:

```text
T09-Abschlussaudit
```

| Virtuelles System | Erstellungszeit |
|---|---|
| `ADM01` | 12.11.2026, 10:53:13 |
| `CL01` | 12.11.2026, 10:53:18 |
| `DC01` | 12.11.2026, 10:53:31 |
| `FS01` | 12.11.2026, 10:53:41 |
| `RTR01` | 12.11.2026, 10:53:42 |

## Ergebnis

Alle fünf geplanten Auditstichproben erfüllten den festgelegten Soll-Zustand. Es wurden keine echten Abweichungen festgestellt, deshalb waren keine Korrekturen oder Nachtests erforderlich. Die wichtigsten technischen Maßnahmen und Vorher-/Nachher-Vergleiche wurden für die Abschlussdarstellung konsolidiert. Der Abschlussstand ist durch den Hyper-V-Checkpoint `T09-Abschlussaudit` gesichert.
