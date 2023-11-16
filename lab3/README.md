# Обработка данных в языке R - 2
Скаев Сармат, БИСО-02-20

## Цель работы

1.  Развить практические навыки использования языка программирования R
    для обработки данных
2.  Закрепить знания базовых типов данных языка R
3.  Научиться работать с пакетом dlpyr

## Ход работы

## Подключим нужные нам пакеты для работы

``` r
library(dplyr)
```

    Warning: пакет 'dplyr' был собран под R версии 4.3.2


    Присоединяю пакет: 'dplyr'

    Следующие объекты скрыты от 'package:stats':

        filter, lag

    Следующие объекты скрыты от 'package:base':

        intersect, setdiff, setequal, union

``` r
library(nycflights13)
```

    Warning: пакет 'nycflights13' был собран под R версии 4.3.2

## Сколько датафреймов в пакете nycflights13?

Их 5

## Сколько строк в каждом из датафреймов?

``` r
nycflights13::weather %>% nrow()
```

    [1] 26115

``` r
nycflights13::planes %>% nrow()
```

    [1] 3322

``` r
nycflights13::airports %>% nrow()
```

    [1] 1458

``` r
nycflights13::airlines %>% nrow()
```

    [1] 16

``` r
nycflights13::flights %>% nrow()
```

    [1] 336776

## Сколько стоблцов в каждом из датафреймов

``` r
nycflights13::weather %>% ncol()
```

    [1] 15

``` r
nycflights13::planes %>% ncol()
```

    [1] 9

``` r
nycflights13::airports %>% ncol()
```

    [1] 8

``` r
nycflights13::airlines %>% ncol()
```

    [1] 2

``` r
nycflights13::flights %>% ncol()
```

    [1] 19

## Как посмотреть на сам датафрейм?

``` r
nycflights13::weather %>% glimpse()
```

    Rows: 26,115
    Columns: 15
    $ origin     <chr> "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EWR", "EW…
    $ year       <int> 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013, 2013,…
    $ month      <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ day        <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1,…
    $ hour       <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 13, 14, 15, 16, 17, 18, …
    $ temp       <dbl> 39.02, 39.02, 39.02, 39.92, 39.02, 37.94, 39.02, 39.92, 39.…
    $ dewp       <dbl> 26.06, 26.96, 28.04, 28.04, 28.04, 28.04, 28.04, 28.04, 28.…
    $ humid      <dbl> 59.37, 61.63, 64.43, 62.21, 64.43, 67.21, 64.43, 62.21, 62.…
    $ wind_dir   <dbl> 270, 250, 240, 250, 260, 240, 240, 250, 260, 260, 260, 330,…
    $ wind_speed <dbl> 10.35702, 8.05546, 11.50780, 12.65858, 12.65858, 11.50780, …
    $ wind_gust  <dbl> NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, 20.…
    $ precip     <dbl> 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0,…
    $ pressure   <dbl> 1012.0, 1012.3, 1012.5, 1012.2, 1011.9, 1012.4, 1012.2, 101…
    $ visib      <dbl> 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10, 10,…
    $ time_hour  <dttm> 2013-01-01 01:00:00, 2013-01-01 02:00:00, 2013-01-01 03:00…

## Сколько компаний перевозчиков учитывает наборы данных?

``` r
nycflights13::airlines %>% nrow()
```

    [1] 16

## Сколько рейсов было принято аэропортом John F Kennedy Intl в мае?

``` r
ans <- nycflights13::airports %>% filter(name == "John F Kennedy Intl") %>% select(faa)
nycflights13::flights %>% filter(month == 5, dest == ans) %>% nrow()
```

    Warning: Using one column matrices in `filter()` was deprecated in dplyr 1.1.0.
    ℹ Please use one dimensional logical vectors instead.

    [1] 0

## Самый северный аэропорт?

``` r
northAirport <- max(nycflights13::airports$lat, na.rm = TRUE)
nycflights13::airports %>% select(name,lat) %>% filter(lat == northAirport)
```

    # A tibble: 1 × 2
      name                      lat
      <chr>                   <dbl>
    1 Dillant Hopkins Airport  72.3

## Самый высокогорный аэропорт?

``` r
nycflights13::airports %>% arrange(desc(alt)) %>% select(name,alt) %>% top_n(1)
```

    Selecting by alt

    # A tibble: 1 × 2
      name        alt
      <chr>     <dbl>
    1 Telluride  9078

## Какие бортовые номера у старых самолётов?

``` r
nycflights13::planes %>% select(tailnum, year) %>% filter(year == min(year, na.rm = TRUE))
```

    # A tibble: 1 × 2
      tailnum  year
      <chr>   <int>
    1 N381AA   1956

## Какая средняя температура воздуха была в аэропорту John F Kennedy Intl в сентябре

``` r
nycflights13::weather %>% filter(month == 9, origin == "JFK") %>% summarise("temperature" = ((temp_mean = mean(temp, 0, na.rm = TRUE))-32)*0.55556)
```

    # A tibble: 1 × 1
      temperature
            <dbl>
    1        19.4

## Самолеты какой авиакомпании совершили больше всего вылетов в июне?

``` r
temp <- nycflights13::flights %>% group_by(carrier) %>% summarise("count_of_flights" = n())
a1 <- arrange(temp, desc(count_of_flights))
top <- top_n(a1, 1)
```

    Selecting by count_of_flights

``` r
filter(nycflights13::airlines, carrier == top$carrier)
```

    # A tibble: 1 × 2
      carrier name                 
      <chr>   <chr>                
    1 UA      United Air Lines Inc.

## Какие самолёты (какой авиакомпании) задержались чаще других в 2013 г.?

``` r
temp1 <- nycflights13::flights %>% group_by(carrier) %>% filter(dep_delay > 0, arr_delay > 0) %>% summarise("counts" = n())
a2 <- arrange(temp1, desc(counts))
top2 <- top_n(a2, 1)
```

    Selecting by counts

``` r
filter(nycflights13::airlines, carrier == top2$carrier)
```

    # A tibble: 1 × 2
      carrier name                    
      <chr>   <chr>                   
    1 EV      ExpressJet Airlines Inc.
