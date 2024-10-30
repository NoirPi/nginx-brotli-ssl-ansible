# ACME.sh Ansible Playbook - Zertifikatverwaltung mit Cloudflare und Nginx

Dieses Ansible-Playbook installiert und konfiguriert das Tool ACME.sh zur Verwaltung von SSL-Zertifikaten für mehrere
Domains, die über die Cloudflare-API validiert und für die Bereitstellung auf Nginx konfiguriert werden. ACME.sh nutzt
die ACME-API und erstellt Zertifikate mit ZeroSSL als CA.

## Inhalt

1. [Voraussetzungen](#voraussetzungen)
2. [Playbook-Übersicht](#playbook-übersicht)
3. [Variablen und Einstellungen](#variablen-und-einstellungen)
4. [Anwendung des Playbooks](#anwendung-des-playbooks)
5. [Anpassung](#anpassung)

## Voraussetzungen

- **Ansible**: Installiere Ansible auf dem Steuerungsknoten (``sudo apt install ansible``).
- **Cloudflare-Konto**: Ein API-Token mit Berechtigungen für DNS-Updates der betroffenen Domains.

## Playbook-Übersicht

Dieses Playbook enthält folgende Hauptfunktionen:

1. **ACME.sh Installation und Konfiguration**: Klont und installiert das ACME.sh-Skript und konfiguriert es für den
   automatisierten Einsatz.
2. **Zertifikatserstellung und -erneuerung**: Erzeugt und erneuert SSL-Zertifikate für jede in der ``domains``-Variable
   angegebene Domain.
3. **Nginx-Konfiguration**: Konfiguriert Nginx, um SSL-Zertifikate zu laden und HTTPS-Traffic abzusichern.
4. **Basic Fail2Ban installation und konfiguration**: Installiert Fail2Ban und konfiguriert ssh und http|https

## Variablen und Einstellungen

### Wichtige Variablen im Playbook


| Variable                        | Beschreibung                                                                                                           | Standardwert         |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------------------- | -------------------- |
| ``nginx_server_header``         | Custom NGINX Server Header                                                                                             | nginx                |
| ``nginx_mainline_version``      | Installiert die mainline Version aus den nginx repos anstatt der release version (mainline aktueller)                  | ``true``             |
| ``nginx_remove_mail_modules``   | Configuriert nginx ohne die flags: [``--with-mail``, ``--with-mail_ssl_module``]                                       | ``true``             |
| ``nginx_remove_stream_modules`` | Configuriert nginx ohne die flags: [``--with-stream``, ``--with-stream_realip_module``, ``--with-stream_ssl_module``] | ``false``            |
| ``acme_sh_path``                | Installationspfad für ACME.sh.                                                                                        | ``/usr/local/sbin``  |
| ``cloudflare_api_key``          | API-Schlüssel für Cloudflare DNS-Updates. [Zone:read, DNS:write]                                                     | -                    |
| ``domains``                     | Liste der zu verwaltenden Domains.                                                                                     | -                    |
| ``wildcard``                    | Aktiviert Wildcard-Zertifikate, wenn auf``true`` gesetzt.                                                              | ``false``            |
| ``generate_domain_configs``     | Wenn``true``, wird für jede Domain eine separate Nginx-Konfiguration generiert.                                       | ``false``            |
| ``cert_path``                   | Pfad, in dem die generierten Zertifikate gespeichert werden.                                                           | ``/etc/ssl/private`` |
| ``acme_sh_dry_run``             | Soll ein acme dry-run durchgeführt werden? (Verhindert Ratelimits von Let's Encrypt)                                  | ``true``             |
| ``account_email``               | Email Adresse für den Let's Encrypt Account                                                                           | -                    |

## Anwendung des Playbooks

1. **Inventar erstellen**: Erstelle eine Inventar-Datei (z. B. ``inventory.ini``) mit dem Server, auf dem die
   Konfiguration angewendet werden soll.
   ```ini
   [all]
   10.1.0.110 ansible_user=root


   ```

## Ausführung:

Zum ausführen des Playbooks nutzen sie den folgenden Befehl im root Verzeichnis des Playbooks:

```bash
ansible-playbook -i inventory.ini setup.yml -vv
```

## Anpassung

Domains: Um zusätzliche Domains hinzuzufügen oder zu ändern, passe die domains-Variable an.

SSL-Konfiguration: Die SSL-Konfiguration in der Nginx-Vorlage (nginx_default_server_conf.j2) kann weiter angepasst
werden, um z. B. verschiedene Sicherheitsheader oder SSL-Cipher zu setzen.

Diffie-Hellman-Parameter: Für zusätzliche Sicherheit kann die Datei dhparam.pem mit einer höheren Schlüssellänge
generiert werden.

Dieses Playbook unterstützt die regelmäßige Erneuerung von Zertifikaten und stellt sicher, dass die Nginx-Konfiguration
stets aktuelle Zertifikate verwendet.
