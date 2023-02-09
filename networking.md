## Podstawy

Sieć- automatyczne połaczenie komputerów (przesyłanie informacje ręcznie np przez pendrive nazywane jest _sneakernet_).

pLAN- personal LAN magistrala która jest konfiguralna.

Rodzaje komunikacji

- punkt-punkt
- rozgłoszeniowa

### Topologie fizyczne

jak połączone są urządzenia sieciowe fizycznie

- Magistrala - urządzneia połączone są jeden do drugiego, node odbierający musi wykonać funkcję terminatora przez signal dampening

- Gwiazdy - routery
- Pierścienia - zamknięta pętle koncetratory mają połaczenia do siebie. Kostefektywna
- Siatki

Topologie logiczne sposób trasmicsji danych. Logiczny łancuch można wykonać na magistrali lub pierścieniu. Logiczna sitaka (każdy z każdym) jest bardzo kosztowna przez prawo Metcalfe'a. Jednak zrobiona ad hoc stanowi podstawę BitTorrent.

## Stos protokołów sieciowych

OSI - open system interconnection najpopularniejszy model.
Ma 7 warstw.
TCP/IP - inaczej zwany modelem internetowym.

Protokoły stosowane w każdej warstwie musza być identyczne w obu urządzeniach. Protokoły komunikacji międzywarstwowej mogą być jednak różne.

Metadane są dołaczone przy transporcie. Dodawanie ich nazywa się _enkapsulacją_ a usuwanie _dekapsulacją_.

Algorytm CRC (cyclic redundancy check)- wszystkie dane odbierane są przetwarzane przez CRC. Algorytm waliduje poprawność odbieranych pakietów
