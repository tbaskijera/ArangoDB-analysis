# Naslov rada

**Autor:** doc. dr. sc. Vedran Miletić

**Datum:** 16. prosinca 2022.

## Prvi podnaslov

Lorem ipsum dolor sit amet, consectetur adipiscing elit. Phasellus sit amet facilisis ipsum. Nunc congue accumsan sagittis. Donec lacinia, leo a consequat laoreet, leo dui suscipit tortor, non dictum risus mi ut nulla. ServeTheHome zaključuje u [1]:

> Očekujemo da će promjene u arhitekturi jezgre Zen 3 biti barem, ako ne i više, važne u domeni poslužitelja nego što su to u domeni osobnih računala. Očekujemo da AMD ima i još nekoliko drugih značajki u rukavu pored danas spomenutih promjena računske jezgre.

Integer ullamcorper venenatis pharetra. Aenean at tortor et enim dictum convallis. Integer sed velit bibendum, tempus urna id, suscipit justo. Phasellus congue tempus orci, in hendrerit urna sagittis cursus.

Iskorištena naredba ljuske:

``` shell
$ docker version
Client:
 Version:           20.10.21
 API version:       1.41
 Go version:        go1.19.2
 Git commit:        baeda1f82a
 Built:             Thu Oct 27 21:30:31 2022
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true
(...)
```

Konfiguracijska datoteka za kompoziciju:

``` yaml
services:
  web:
    build: .
    ports:
      - "8000:5000"
    volumes:
      - .:/code
      - logvolume01:/var/log
    depends_on:
      - redis
  redis:
    image: redis
volumes:
  logvolume01: {}

```

## Drugi podnaslov

Donec varius sit amet neque posuere sodales. Aenean rutrum est id consectetur aliquet. Nunc eget tortor vel augue consectetur feugiat. Curabitur ultricies tempus velit eu posuere. Donec non urna tincidunt, gravida ipsum vitae, porta nunc. Aliquam felis neque, rutrum varius vulputate vitae, semper quis lacus. Donec convallis feugiat mi ut aliquet [2].

## Literatura

- [1] <https://www.servethehome.com/amd-zen-3-and-amd-ryzen-5000-series-launch-event/>
- [2] <https://www.nextplatform.com/2020/10/06/porting-to-amd-gpus-in-the-corona-age/>
