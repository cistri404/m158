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

![Screenshot 2025-06-18 164621](https://github.com/user-attachments/assets/a18ed864-5c28-4507-b6d7-177e71fdcb29)



## Aufgabe 7 - MySQL/MariaDB-Datenbankserver

### Stufe 1 - 3

Zuerst habe ich mich auf meiner zweiten Instanz in der Umgebung verbunden (Den Datenbankserver).
Auf dieser Insatz habe ich MariaDB installiert und in der Config die externe Erreichbarkeit angemacht. (Listen-IP von 127.0.0.1 to 0.0.0.0)

Dann habe ich einen 
