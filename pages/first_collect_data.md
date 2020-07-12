---
layout: page
title:  "Zbieranie danych"
date:   2020-07-12 20:07:49 +0200
---

## Pobieranie i zbieranie pierwszych danych

Dzięki udostępnionemu przez MPK Kraków interfejsie programistycznym, przy uzyciu kilku endpointów jesteśmy w prosty sposób wydobyc informacje o konkretnej linii autobusowej, przystankach na jej trasie, oraz spóźnieniach autobusów na wybranych przystankach. 

Przykładowo odpytując [ten endpoint](http://91.223.13.70/internetservice/services/routeInfo/routeStops?routeId=8095257447305839175) uzyskamy iformacje o przystankach na trasie autobusu linii `139`. Natomiast zapytania na [ten](http://91.223.13.70/internetservice/geoserviceDispatcher/services/stopinfo/stops?left=-658000000&bottom=-324000000&right=648000000&top=324000000) adres zdobędziemy dane na temat wszystkich przystanków, co w połączeniu z wcześniejszym zapytaniem pozwala nam uzyskac dokładne informacje, np. o połozeniu konkretnych przystanków na trasie linii `139`.
Przykładowa realizacja w języku `Python`:

```Python
import requests


STOPS_139 = [stop['number'] for stop in requests.get(ROUTE_139_URL).json()['stops']]

STOPS_139_COORDINATES = {
    stop['shortName']:{'latitude': stop['latitude'], 'longitude':stop['longitude']} for stop in requests.get(STOPS_URL).json()['stops'] if stop['shortName'] in STOPS_139
}

```
W wyniku działania powyzszego skryptu uzyskujemy mapę zawierającą identyfikator przystanku i odpowiednie współrzędne geograficzne (`latitude` i `longitude`) na trasie linii `139`.

### Jakich danych potrzebujemy?

Wiemy juz, ze dostępne API dostarcza nam wiele mozliwosci, jednak jakie konkretne dane potrzebujemy? 

Naszym celem będzie zdobycie takich danych, które pozwolą na predykcje opóźnień w danym dniu tygodnia, o danej godzinie, na określonym przystanku oraz przy konkretnym natęzeniu ruchu na odcinku drogi w poblizu przystanku. 

Dodatkowo dodalismy dwa rekordy opisujące pogode i temperaturę w Krakowie w chwilii pomiaru - przykładowo deszczowa pogoda, moze wpłynąc na utrudnienia w ruchu, a co za tym idzie spóźnienia mogą wzrosnąc. W tym przypadku skorzystamy z [API pogodowego](https://openweathermap.org/api).

Natomiast informacje o natęzeniu ruchu na odcinku drogi zdobędziemy dzięki API [Azure Maps](https://docs.microsoft.com/en-us/rest/api/maps/traffic/gettrafficflowsegment#definitions).


### Skrypt do pobierania danych

W naszym skrypcie (link do pobrania [tutaj](https://github.com/Valaraucoo/bus_data_scrapper/)) stale monitorujemy stan pojazdów na przystanku - jeśli autobus dotarł na przystanek sprawdzamy, jaka była róznica czasu między realnym przyjazdem, a planowanym przyjazdem - natomiast, jesli chodzi o pozostałe dane to co kilka iteracji odświezamy informacje o pogodzie i natęzeniu ruchu. 

Teorytycznie API TTSS, jest dosyc wydajne, jednak w praktyce nie mozemy zbyt szybko wysyłac zapytań do wielu endpointów. Jednak jest to kwestia [łatwo konfigurowalna](https://github.com/Valaraucoo/bus_data_scrapper/blob/master/README.md), jeśli ktoś by chciał na własną rękę zmienic parametry pobierania danych.


### Zbieranie danych
...
