# Sega Nomad Switchless Mod - Arduino Nano Version

Dieses Projekt ist eine Portierung des originalen Saturn Switchless Mods für den **Sega Nomad** mit einem **Arduino Nano** und einem **128x32 OLED Display**.

## Hardware-Anforderungen

- **Arduino Nano** (ATmega328P)
- **128x32 OLED Display** (SSD1306, I2C)
- **Taster** für Regionswechsel
- **Verbindungskabel** für Nomad-Anschluss

## Pin-Belegung

### Arduino Nano Anschlüsse:

| Funktion | Arduino Pin | Beschreibung |
|----------|-------------|--------------|
| Button | Pin 2 | Taster für Funktionen (mit internem Pull-up) |
| OLED SDA | Pin A4 | I2C Datenleitung |
| OLED SCL | Pin A5 | I2C Taktleitung |
| OLED VCC | 3.3V/5V | Stromversorgung Display |
| OLED GND | GND | Masse |

### Sega Nomad Anschlüsse:

| Funktion | Arduino Pin | Nomad Anschluss | Beschreibung |
|----------|-------------|-----------------|--------------|
| JP1 | Pin 3 | Nomad JP1 | Region Jumper (0=English, 1=Japanese) |
| JP3 | Pin 4 | Nomad JP3 | Video Format Jumper (0=60Hz, 1=50Hz) |
| VF | Pin 6 | Video Format | Zusätzliche 50/60Hz Steuerung |
| RST | Pin 7 | Reset | Reset-Signal für Nomad |

## Funktionen

### Button-Steuerung:
- **Kurzer Druck** (< 0,3s): Reset auslösen
- **Mittlerer Druck** (0,3-1,5s): 50/60Hz umschalten
- **Langer Druck** (> 1,5s): Region wechseln (EU → USA → JAP → EU...)

### Display-Anzeige:
- **Sega Nomad Logo**: Standard-Anzeige mit aktueller Region und Hz-Einstellung
- **Regions-Anzeige**: Nach Regionswechsel wird 5 Sekunden die neue Region angezeigt
- **Video Format**: Bei Hz-Umschaltung wird das neue Format angezeigt

### Regionen:
- **EU**: 50Hz, JP1=0 (English), JP3=1 (50Hz)
- **USA**: 60Hz, JP1=0 (English), JP3=0 (60Hz)
- **JAP**: 60Hz, JP1=1 (Japanese), JP3=0 (60Hz)

## Installation

### PlatformIO:
1. Projekt in PlatformIO öffnen
2. `pio run` zum Kompilieren
3. `pio run --target upload` zum Hochladen

### Arduino IDE:
1. Benötigte Bibliotheken installieren:
   - Adafruit SSD1306
   - Adafruit GFX Library
   - Adafruit BusIO
2. Sketch kompilieren und hochladen

## Verdrahtung im Sega Nomad

**ACHTUNG**: Diese Modifikation erfordert Löt-Kenntnisse und kann bei unsachgemäßer Durchführung das Gerät beschädigen!

### Jumper-Anschlüsse:
Die originalen Jumper JP1 und JP3 müssen entsprechend modifiziert werden:

- **JP1 (Region)**: 0 = English (EU/USA), 1 = Japanese  
- **JP3 (Video)**: 0 = 60Hz, 1 = 50Hz

### Video Format:
Der VF-Pin steuert die 50/60Hz Umschaltung der Videoausgabe.

### Reset:
Der Reset-Pin kann das Nomad neu starten (nützlich nach Regionswechsel).

## Logo-Integration

Um das Sega Nomad Logo auf dem Display anzuzeigen:

1. **Logo vorbereiten**: 
   - Erstellen Sie ein 128x32 Pixel Bitmap (schwarz/weiß)
   - Speichern als BMP, PNG oder ähnliches

2. **Konvertieren**:
   - Verwenden Sie Tools wie "LCD Image Converter" oder Online-Konverter
   - Exportieren als C-Array für Arduino/Adafruit GFX

3. **Integration**:
   - Fügen Sie das Array in `main.cpp` ein
   - Ersetzen Sie die Text-basierte Logo-Anzeige durch `display.drawBitmap()`

**Beispiel für Logo-Array:**
```cpp
const unsigned char nomadLogo[] PROGMEM = {
  0x00, 0x00, 0x00, 0x00, // ... Ihre Logo-Daten
};

// In der showNomadLogoBitmap() Funktion:
display.drawBitmap(0, 0, nomadLogo, 128, 32, SSD1306_WHITE);
```

## Unterschiede zum Original

- **Kein PIC-Controller**: Verwendet Arduino Nano statt PIC16F630/676
- **OLED Display**: Visueller Status statt LED-Anzeigen
- **Erweiterte Funktionen**: Mehr Informationen auf Display
- **I2C-Kommunikation**: Für OLED-Ansteuerung

## Lizenz

Basiert auf dem originalen Saturn Switchless Mod von Sebastian Kienzl.  
Portierung unter gleicher GPL v2 Lizenz.

## Troubleshooting

- **Display zeigt nichts**: I2C-Verkabelung prüfen (SDA/SCL)
- **Region wechselt nicht**: Jumper-Verdrahtung kontrollieren
- **Reset funktioniert nicht**: RST-Pin Verbindung prüfen