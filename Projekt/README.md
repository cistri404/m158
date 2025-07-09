# Projektdokumentation – M158 LB2 <br>WordPress-Migration

## Übersicht
# Projektdokumentation – Webserver-Projekt

Diese Dokumentation ist nach den Aufgaben (1–14) gegliedert. Jede Aufgabe ist in drei Phasen unterteilt. Bitte tragen Sie Ihre Ergebnisse jeweils unter den entsprechenden Abschnitten ein.

---

## Aufgabe 1 – Projektplan erstellen

### Stufe 1 - 3
Den Projektplan habe ich über das Online-Tool "Tomsplanner" erstellt:
![image](https://github.com/user-attachments/assets/504569ec-5698-48c1-aeaf-5817be41fbf0)

*https://plan.tomsplanner.com/#doc=WBnUTMsSTcwgZFHvCGcm*

## Aufgabe 2 - Architekturdiagramm

## Aufgabe 3 - Umgebung aufbauen/einrichten

### Stufe 1

Die Umgebung habe ich anhand [dieser Anleitung](https://gitlab.com/ch-tbz-it/Stud/m158/-/tree/main/04_Unterrichtsressourcen/00_AWS/AWS-Netzwerk-Anleitung) vom Lehrer eingerichtet mit einzelnen Veränderungen. Dazu habe ich die beiden Instanzen in einem Webserver und einem Datenbankserver getrennt.

Innerhalb der VMs habe ich dann die Netzwerkumgebung getestet:
![Screenshot 2025-06-02 102516](https://github.com/user-attachments/assets/370f8d62-0ec7-4947-84bc-4fcbcfd9cc29)
![Screenshot 2025-06-02 102522](https://github.com/user-attachments/assets/1f125568-7241-4da6-8041-cf596f21c4e3)

## Aufgabe 4 - DNS

### Stufe 2

Die DNS Aufgabe habe ich über meine private Domain gemacht. Dazu habe ich ganz einfach in meinem Nameserver (Cloudflare) ein DNS Eintrag mit der Subdomain "m158" hinzugefügt und auf die elastische IP Adresse des Webservers gelinkt.
![image](https://github.com/user-attachments/assets/a6a8f06c-72be-4634-a5d7-c9c98f5f75bb)

## Aufgabe 5 - Webserver

### Stufe 1 - 3

Ich habe für den Webserver 2 VirtualHost Files von Apache erstellt.

Das erste VirtualHost File schaut auf den Port 443 (HTTPS) und checkt ob die Domain = m158.famrade.ch ist. Wenn alles passt, leitet er den User zum Web Root Folder für das m158 Projekt.
Ausserdem fügt er den self-signed Certificate bei der Verbindung ein.

![Screenshot 2025-06-18 164504](https://github.com/user-attachments/assets/350b70fe-f969-4a00-a945-e56924e1a9d4)

Das zweite VirtualHost File schaut auf den Port 80 (HTTP) und checkt auch ob die Domain = m158.famrade.ch ist. Falls dies der Fall ist, leitet er den User zum HTTPS Link um (SSL).

![Screenshot 2025-06-18 163521](https://github.com/user-attachments/assets/4ef0f1c6-971c-4552-9f9c-c754b88dc7ff)

Zuletzt wurde noch die Default Apache Page entfernt und die Rewriteengine per htdocs angepasst. In dieser RewriteEngine werden Anfragen die zu einer nicht-existenten Datei wollen nach index.php weitergeleitet.
![Screenshot 2025-06-18 162917](https://github.com/user-attachments/assets/2df5be8b-5a28-4dd2-a919-15c7a681e76b)


## Aufgabe 6 - PHP

### Stufe 1 - 3

Zuerst habe ich die empfohlene PHP Version für Wordpress gesucht und mich anhand [dieser Website](https://make.wordpress.org/core/handbook/references/php-compatibility-and-wordpress-versions/) auf die PHP Version 8.2 entschieden.

Danach habe ich die FastCGI Version von dieser PHP Version installiert (php-fpm8.2).

Zuletzt habe ich in der FastCGI Config alle empfohlenen PHP Einstellungen für Wordpress angepasst. Die empfohlenen PHP Einstellungen habe ich auf einer Website gefunden.

```
memory_limit = 256M
upload_max_filesize = 64M
post_max_size = 64M
max_execution_time = 120
display_errors = Off
```

Hier habe ich dann alles getestet.

![Screenshot 2025-06-18 164621](https://github.com/user-attachments/assets/a18ed864-5c28-4507-b6d7-177e71fdcb29)



## Aufgabe 7 - MySQL/MariaDB-Datenbankserver

### Stufe 1 - 3

Zuerst habe ich mich auf meiner zweiten Instanz in der Umgebung verbunden (Den Datenbankserver).
Auf dieser Insatz habe ich MariaDB installiert und in der Config die externe Erreichbarkeit angemacht. (Listen-IP von 127.0.0.1 to 0.0.0.0)

Dann habe ich einen Wordpress User erstellt und ihm die Berechtigungen gegeben, die er braucht:

![Screenshot 2025-06-18 164912](https://github.com/user-attachments/assets/74247cff-7984-4007-950f-fce58959c7f7)

Danach habe ich das Passwort vom Root User geändert. Uneingeschränkte Berechtigung hat er bereits.

![Screenshot 2025-06-18 165052](https://github.com/user-attachments/assets/59af2c3d-6923-408a-8c19-f860ab801b52)


## Aufgabe 8 - PhpMyAdmin

Die PhpMyAdmin Seite habe ich wieder auf den Webserver installiert mit dem apt-Befehl. Die Nebeninstallation einer MySQL DB habe ich abgelehnt und im Nachhinein in der Config von PhpMyAdmin die DB Angaben mit externem Host eingerichtet. 

Um PHPMyAdmin nun noch über die Subdomain verfügbar zu machen, wurde folgendes Feld in der SSL VirtualHost hinzugefügt. Dieser Feld sagt, dass Anfragen die den /phpmyadmin Subfolder aufrufen, zum internen Ordner /usr/share/phpmyadmin geleitet werden.

![Screenshot 2025-06-18 170714](https://github.com/user-attachments/assets/9de18a21-faa2-44fb-aade-0bfaf303452a)


Zuletzt wurde dies noch erfolgreich getestet:

![Screenshot 2025-06-18 170705](https://github.com/user-attachments/assets/7632d4b9-e44e-4f63-92d4-efbba1063da8)


## Aufgabe 9 - FTP-Server

Den FTP Server habe ich über vsftpd installiert. Das Paket war einfach zu installieren und die Config habe ich durch diverse Dokumentationen und ChatGPT-Anfragen ausfüllen können.

Ein FTP Benutzer habe ich durch die normale Linux Benutzer Einrichtung hinzugefügt und ihm Rechte auf den Root Folder für das Projekt gegeben.

Danach habe ich ohne Zwischentest direkt auch FTPS eingerichtet. Hier kam nun mein grosser Zeitverschwender. Jede FTPS Verbindung wurde denied und der Fehler war nicht richtig zu identifizieren. Nach einigen Stunden rumversuchen habe ich es mal ohne FTPS versucht und konnte identifizieren, dass das Problem nicht an FTPS lag. FTPS hat die Fehlernachricht obfuskiert und daher wurde davor eine fehlerhafte Fehlermeldung angezeigt. Nach dem temporären Entfernen von FTPS konnte ich herausfinden, dass das Problem an unzureichender Rechte vom ftpuser lag. Nach Hinzufügen der nötigen Rechte und wiederverbindung von FTPS funktionierte dann alles.

![Screenshot 2025-06-19 094259](https://github.com/user-attachments/assets/a33fce02-7ac7-4cc0-909a-6a47d7f700a2)

## Aufgabe 10 - Wordpress Migration

### Stufe 1 - 3

Zuerst habe ich mich per FTP auf den Server vom Lehrer verbunden und habe die Wordpress Daten auf meinem lokalen Notebook heruntergeladen.
Das SQL File habe ich im Datenbankserver importiert.

Die restlichen Dateien habe ich auf den Webserver in den Projektordner reingepackt.
In der wp-config.php Datei habe ich dann die Datenbankserver Details eingerichtet.

Dann habe mich testweise mal per Website darauf verbunden.
Die Website hat mich dann zur alten Website vom Lehrer weitergeleitet.

Ich musste 2 Anpassungen in der DB machen (WP_HOME und WP_SITEURL zu meiner Domain wechseln). Nach diesen Anpassungen wurde ich nicht mehr weitergeleitet und war nun auf der neuen Wordpress Instanz meines AWS Servers.

![Screenshot 2025-06-19 160304](https://github.com/user-attachments/assets/3c6c3e19-def8-466c-af7a-20a6278b6a9a)


## Aufgabe 11 - Backup

### Stufe 1 - 3

Ich habe 2 BASH Files erstellt.
In der Datei für den Webserver wird eine tar.gz Datei aus dem wp-content Ordner gemacht und in /var/backups/wp-content gespeichert. Der Name wird anhand vom Backup-Datum angepasst. Alte Backups werden gelöscht.

![Screenshot 2025-06-19 141614](https://github.com/user-attachments/assets/d1f0a667-0bb0-4753-9b6b-5d7e411def35)

In der Datei für den Datenbankserver wird eine .gz Datei aus einem mysqldump der wordpress_db Datenbank erstellt. Dieser wird dann auch im Ordner /var/backups/mysql gespeichert und der Name wird auch anhand vom Backup-Datum angepasst. Alte Backups werden gelöscht.

![Screenshot 2025-06-19 141705](https://github.com/user-attachments/assets/53e46635-3877-4ccd-a272-efc68bdf6b40)

Bei beiden Files wird am Schluss eine E-Mail per msmtp an meine private E-Mail Adresse gesendet. Der Mailserver ist bereits bestehend auf einem meiner Homeserver. MSMTP hat in diesem Fall nur die Verbindung von Linux zum externen Mailserver übernommen.
Beide Files wurden dann in crontab eingerichtet und werden jede Nacht ausgeführt.

![Screenshot 2025-06-19 103743](https://github.com/user-attachments/assets/481c2c2e-bbe7-4d00-86a2-599919755c6a)
![Screenshot 2025-06-19 141647](https://github.com/user-attachments/assets/8f10c513-8eea-45de-b8bb-b4ab6b54f4fc)

Am Schluss habe ich das ganze noch erfolgreich getestet:

![Screenshot 2025-06-19 142143](https://github.com/user-attachments/assets/ae93a032-3568-45c0-b0c7-6329b3811edf)
![Screenshot 2025-06-19 141751](https://github.com/user-attachments/assets/acebf69a-0eaa-463c-b356-b71f14b18e46)

## Aufgabe 12 - Testing

Zuerst habe ich ein Testkatalog mit 10 Testfällen mithilfe von ChatGPT erstellt und nach der Migration alles nacheinander getestet.
Fehler habe ich gekennzeichnet und danach behoben.

![Screenshot 2025-06-19 152219](https://github.com/user-attachments/assets/8383bc91-9fb1-4bea-9fbd-cd7d7099c92a)

Danach wurden auch alle Probleme in den Site-Health Seiten behoben:

