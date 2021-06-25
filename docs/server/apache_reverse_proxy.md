# Apache Reverse Proxy
## Was ist ein Reverse Proxy?
Einen Reverse-Proxy kann man z.B. dazu nutzen, um an einem privaten Internetanschluss mit nur einer öffentlichen IPv4-Addresse mehrere Werbserver abhängig von verschiedenen Domains auf verschiedenen Ports / internen IP-Addressen bereitstellen zu können.

Beispiel:
Du hast einen Webserver auf einem Raspberry PI, auf einem weiteren PI hast du Bitwarden installiert und möchtest nun dieses über den 1. PI (auf dem weitere Webseiten laufen - abhängig von Domains) erreichen, da man nur je Port an eine IP weiterleiten kann. Um nun abhängig von der verwendeten Domain (z.B. bitwarden.jannis6023.tk) Bitwarden trotzdem erreichen zu können, kannst du einen Reverse-Proxy verwenden!

## Schritt-für-Schritt-Tutorial
Dieses Tutorial ist für Ubuntu 20.04 ausgelegt.
### Schritt 1: Installiere Apache und die aktiviere die nötigen Modules
```shell
apt install apache2
a2enmod mod_proxy
```

### Schritt 2: Installiere Certbot für Letsencrypt
```shell
apt install certbot python3-certbot-apache
```

### Schritt 3: Erstelle und aktiviere den ersten VirtualHost
Erstelle zunächst je VirtualHost eine neue Datei - am Besten mit dem Befehl `nano <Dateiname>`.

Navigiere zum Verzeichnis `/etc/apache2/sites-available/` mit dem Befehl `cd /etc/apache2/sites-available/`.
Bearbeite / Erstelle nun eine neue Datei mit der Dateiendung `.conf`, dem Namen deiner Domain, wobei `.` durch `_` ersetzt wird, also z.B. `docs_jannisgollwitzer_de.conf` mit dem Befehl `nano docs_jannisgollwitzer_de.conf`.
Füge nun in diese Datei den folgenden Code / Text ein:

```apacheconf {2,6,7}
<VirtualHost *:80>
 ServerName subdomain.domain.tld
 ProxyPreserveHost On
 DocumentRoot /var/www/html
 ProxyPass /.well-known !
 ProxyPass / http://deine.ip.addresse.hier:80/
 ProxyPassReverse / http://deine.ip.addresse.hier:80/
</VirtualHost>
```

**Für jede Domain, die weitergegeben werden soll, verfahre einfach genau so wie hier beschrieben und ändere die hervorgehobenen Zeilen auf deine Daten ab!**

Um nun den / die VirtualHost(s) zu aktivieren, verwende diesen Befehl:
```shell
a2ensite <Konfigurationsdateiname>
```

Lade nun den Apache-Server neu, um die Konfiguration zu übernehmen:
````shell
systemctl reload apache2
````

### Schritt 4: Sichere die Verbindung mit SSL ab
Damit Vebindungen zu deinem Server SSL-Verschlüsselt sind, benötigst du ein Zertifikat. Ich empfehle die kostenlose Letsencrypt-Zertifikate:

```shell
certbot
```

Gib dort zunächst alle gefragten Informationen nach deinem Ermessen an und wähle dann anhand der Zahlen die Domains, für die ein Zertifikat erstellt werden soll!

:::caution
Bitte richte zunächst deine Nameserver-Einträge korrekt ein! Ansonsten können die Zertifikate nicht erstellt werden!
:::

Nachdem du die Domains gewählt hast, lasse nun durch Wahl der Zahl 2 (-> Redirect) eine Weiterleitung von `HTTP` auf `HTTPS` einrichten, sobald danach gefragt wird!

---

Nun sollte die Einrichtung des Reverse Proxies abgeschlossen sein. Wenn du mehrere Proxy-Hosts benötigst, verfahre ebenso mit jedem VHost!

:::note
Solltest du noch Fragen haben, wende dich gerne per Discord an mich - mein Tag lautet: `Jannis#6023`
:::