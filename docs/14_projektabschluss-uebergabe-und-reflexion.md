# Projektabschluss, Übergabe und Reflexion

## Abschlussdarstellung

### Ausgangslage

Zu Projektbeginn waren Client-, Server- und Router-Systeme vorhanden, administrative Zugriffe waren jedoch noch nicht vollständig zentralisiert und vom normalen Clientverkehr getrennt. Härtungs-, Firewall-, Logging- und Wiederherstellungsmaßnahmen mussten technisch umgesetzt und auf ihre Wirkung geprüft werden.

### Zielzustand

Der administrative Zugriff erfolgt zentral über `ADM01` im Managementnetz. Direkte Verwaltungszugriffe aus dem Clientnetz sind blockiert. Die wesentlichen Windows- und Debian-Härtungsmaßnahmen sowie Sicherungs- und Wiederherstellungsverfahren wurden technisch geprüft.

## Sicherheitsentscheidungen

### Zentraler Administrationsweg

**Risiko:** Administrative Dienste waren aus dem normalen Clientnetz erreichbar und vergrößerten dadurch die Angriffsfläche.

**Maßnahme:** Administration über `ADM01` im Managementnetz `10.77.30.0/24`; direkte Verwaltung aus dem Clientnetz wird durch das Firewall-Regelwerk blockiert.

**Wirkung:**

```text
CL01  -> RTR01 / SSH = blockiert
CL01  -> FS01  / RDP = blockiert
ADM01 -> RTR01 / SSH = erlaubt
ADM01 -> FS01  / RDP = erlaubt
```

### SSH-Härtung des Routers

**Risiko:** Eine direkte privilegierte Anmeldung als `root` erhöht die Auswirkungen kompromittierter Zugangsdaten.

**Maßnahme:** Direkten SSH-Root-Zugang deaktivieren und Administration über `labadmin` durchführen.

**Wirkung:**

```text
permitrootlogin no
passwordauthentication yes
```

## Technische Nachweise

Für die Abschlussdarstellung wurden drei vorhandene technische Nachweise ausgewählt:

1. Vergleich der administrativen Erreichbarkeit aus `CL01` und `ADM01`.
2. Effektive SSH-Konfiguration von `RTR01` mit `permitrootlogin no`.
3. SHA-256-Vergleich von Original, Sicherung und wiederhergestellter `nftables`-Konfiguration.

Für die Konfigurationswiederherstellung wurde folgende identische Prüfsumme ermittelt:

```text
72c0f19a989e54511f4d221459608505795f5d56b1756f87f1ff85495def9df5
```

## Kundenübergabe

### System- und Betriebsinformationen

| Bereich | Übergabestand |
|---|---|
| Architektur und Netze | Client-, Server- und Managementnetz sowie Routing über `RTR01` dokumentiert |
| Berechtigungen | LAPS, Dienstkonto und Datei-/Gruppenberechtigungen dokumentiert |
| Härtung und Firewall | `nftables`, Default-Deny und SSH-Härtung dokumentiert |
| Administrationsweg | Zentrale Administration über `ADM01` dokumentiert und geprüft |
| Betrieb und Restore | Logging, Patchprüfung, Backup/Restore sowie RTO/RPO geprüft |
| Wiederherstellung | Recovery-Zugriff über Hyper-V VMConnect verfügbar |

### Regulärer Administrationsweg

```text
ADM01 / 10.77.30.10
  -> RTR01 über SSH
  -> DC01 über RSAT
  -> FS01 über WinRM / RDP
```

### Recovery-Weg

```text
Remote-Lab-Host -> Hyper-V VMConnect -> jeweilige VM
```

Der Recovery-Weg bleibt unabhängig vom regulären Managementnetz verfügbar.

### Sicherungsstände

Der technische Abschlussaudit wurde zuvor als

```text
T09-Abschlussaudit
```

gesichert.

Der abschließende Projektstand wurde anschließend für alle fünf virtuellen Systeme als

```text
Finaler-Projektstand
```

gesichert:

| Virtuelles System | Erstellungszeit |
|---|---|
| `ADM01` | 12.11.2026, 11:00:21 |
| `CL01` | 12.11.2026, 11:00:27 |
| `DC01` | 12.11.2026, 11:00:39 |
| `FS01` | 12.11.2026, 11:00:49 |
| `RTR01` | 12.11.2026, 11:00:50 |

## Management-Zusammenfassung

1. Administrative Zugriffe sind vom Clientnetz getrennt und über einen zentralen Administrationsweg im Managementnetz abgesichert.
2. Die wesentlichen Härtungs- und Firewall-Maßnahmen wurden technisch geprüft und sind wirksam.
3. Backup-, Restore- und Recovery-Verfahren wurden praktisch validiert und für den Betrieb nachvollziehbar dokumentiert.

## Restrisiken und nächste Schritte

- Verfügbare Betriebssystem- und Paketaktualisierungen sollten weiterhin regelmäßig bewertet, getestet und kontrolliert eingespielt werden.
- Logging, Restore-Fähigkeit und administrative Erreichbarkeit sollten als wiederkehrende Betriebsprüfungen fortgeführt werden.

## Persönliche Reflexion

### Technische Entscheidung

Die wichtigste technische Entscheidung war die Trennung administrativer Zugriffe vom Clientnetz. Durch `ADM01` im Managementnetz werden administrative Verbindungen zentral geführt und die Angriffsfläche des normalen Clientnetzes reduziert.

### Diagnosefall

**Symptom:** `Get-ADDomain` funktionierte auf `ADM01` nicht.

**Ursache:** `Active Directory Web Services` auf `TCP 9389` war im Firewall-Regelwerk noch nicht für `ADM01` freigegeben.

**Hilfreichster Diagnoseschritt:**

```powershell
Test-NetConnection DC01.klemmbausteine.intern -Port 9389
```

Der Test grenzte die Ursache direkt auf die fehlende Erreichbarkeit von ADWS ein.

### Beruflicher Transfer

Die Vorgehensweise lässt sich auf die Einführung, Absicherung und Übergabe realer Windows-/Linux-Serverumgebungen übertragen: Soll-Zustand definieren, Änderungen kontrolliert umsetzen, technische Wirkung prüfen, Abweichungen gezielt diagnostizieren und die Wiederherstellbarkeit praktisch testen.

### Persönliche Lernziele

1. `nftables` und Stateful-Firewall-Regeln sicherer planen und Fehler in Regelwerken schneller diagnostizieren.
2. Windows-Remoteadministration mit RSAT, WinRM, ADWS und PowerShell weiter vertiefen.

## Abschluss

Der Projektstand ist technisch geprüft, für die Übergabe konsolidiert und als `Finaler-Projektstand` gesichert. Der reguläre Administrationsweg, der Recovery-Weg, die zentralen Sicherheitsentscheidungen und die wichtigsten technischen Nachweise sind für die Übergabe nachvollziehbar dokumentiert.
