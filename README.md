# 🎮 Projekt: Remote Gaming & AI Server

Denna README fungerar både som en teknisk tutorial och en dokumentation över projektets gång, inklusive hur vi löste de hinder vi stötte på längs vägen.

## 🛠 Projektöversikt

Målet med projektet var att förvandla en dator med **Bazzite** (en immutable Linux-distro) och ett **AMD RX 6800** till en kraftfull streamingserver. Genom att använda **Sunshine** och **Moonlight** skapades en lösning för att spela tunga titlar över 5G-nätet med minimal latens.



## ⚙️ Hårdvaru- och nätverksstack

| Komponent | Vald lösning |
| :--- | :--- |
| **Operativsystem** | Bazzite (KDE Plasma / Wayland) |
| **GPU** | AMD Radeon RX 6800 |
| **Router** | ASUS TUF-BE6500 (WoL-stöd) |
| **VPN** | Tailscale (Säker tunnel över 5G) |
| **Headless-fix** | DisplayPort Dummy Plug |

---

## 🏗 Resans gång: Från hinder till lösning

### 1. BIOS & Strömhantering (Wake-on-LAN)
Första steget var att säkerställa att datorn kunde väckas på distans. Vi konfigurerade BIOS för att tillåta "Magic Packets" och säkerställde att nätverkskortet inte tappade strömmen vid avstängning.
* **ErP Ready:** Sattes till *Disabled*.
* **Power On By PCI-E:** Sattes till *Enabled*.

### 2. Hantering av Immutable OS (Rättighetsproblemet)
Eftersom Bazzite använder ett skrivskyddat filsystem misslyckades traditionella försök att ge Sunshine rättigheter via `setcap` (felet: *"Read-only file system"*).

> **Lösning:** Vi kringgick detta genom att lägga till användaren i grupperna `video` och `render`. Detta gav Sunshine behörighet att läsa bildbufferten och använda grafikkortets hårdvarukodare utan att röra systemfilerna.

### 3. Kampen mot "Error 0" & Headless-problemet
Ett stort bakslag var när streamen dog så fort den fysiska TV:n stängdes av (Moonlight felkod: **Error 0**). Utan en aktiv skärm signal "somnar" grafikkortets encoder.

* **Försök 1 (Misslyckat):** Skapa virtuella skärmar via mjukvara. Detta misslyckades då DBus-gränssnittet i Bazzite returnerade `UnknownInterface`.
* **Försök 2 (Framgång):** Installation av en fysisk **DisplayPort Dummy Plug**. Genom att ställa in TV:n som *Primary* i KDE men låta pluggen vara aktiv, kan Sunshine filma pluggen även när TV:n är svart.

---

## 🚀 Slutgiltig Tutorial: Steg-för-steg

### Steg 1: Ge behörigheter
Öppna terminalen och kör följande kommando för att ge de rättigheter som krävs:

(sudo usermod -aG video,render $USER)

*Starta därefter om datorn för att grupperna ska laddas in korrekt.*

### Steg 2: Konfigurera Sunshine
1. Logga in på webbgränssnittet via `https://localhost:47990`.
2. Navigera till **Configuration** -> **Audio/Video**.
3. Leta upp ID för din dummy-plugg under fliken *Troubleshooting* (ofta `id: 2`).
4. Skriv in detta ID i rutan **Display Id**.

### Steg 3: Säkra skärmprioriteringen
När dummy-pluggen ansluts kan TV:n råka bli svart.
1. Anslut till datorn via mobilen.
2. Gå till **Systeminställningar** -> **Display Configuration**.
3. Markera din Samsung-TV som **Primary**.
4. Klicka på **Apply** för att spara layouten.

### Steg 4: Fjärrstart via 5G
För att spela utanför hemmet:
1. Använd **Tailscale** på din klient för att etablera en säker anslutning till datorn.
2. Skicka en WoL-signal via din ASUS-app för att väcka datorn från strömsparläge.
3. Anslut via **Moonlight** och njut av streamingen!
