Данная работа выполнялась в рамках курса OTUS "Game QA Engineer"
***
# Получить навыки тестирования сайта игры
### Задание

Протестируйте личную страницу игрока в игре https://kor.ru/ :
1. Составьте список видов тестирования и обоснуйте каждый
2. Составьте mind map личного кабинета с обязательными пометками - веб часть , сервер. Прорисуйте эти части желтым цветом/шрифтом (веб) и синим (сервер)
3. В случае портирования данной игры в социальную сеть - какие части функциональности критично проверить (отметьте данные области зеленым цветом/шрифтом)

### Выполнение
1.
* Функциональное:	Проверяем, что все функциональности личного кабинета работают, согласно требованиям. 
* UI/UX: Проверяем для разного окружения из разных платформ, с разным разрешением экрана, размером окна. Что все элементы страницы соответствуют требованиям дизайна и игроку удобно пользоваться ими.
* Совместимость	</br>
  *Кросс-браузерное*:	Обязательно для любой браузерной игры. Проверку проводим, предварительно собрав статистику: на самых популярных браузерах в целом и на самых популярных браузерах нашей целевой аудитории. Обязательно на своем браузере Kor Browser</br>
  *Кросс-платформенное*:	Обязательно для любой браузерной игры. Проводим на платформах, на сборках ПК и ОС, наиболее популярных для нашей целевой аудитории.
* Performance:	Как быстро открывается страница, как быстро открываются ссылки из личной страницы, как быстро информация в лк обновляется относительно игры, нагрузочное тестирование, скорость и типы интернет-соединений
* Безопасность:	На странице есть поля ввода - проверить, что игрок не сможет воспользоваться ими для введения невалидных данных, SQL-injections и XSS

2. Ссылка на майнд-мап в Miro
https://miro.com/app/board/uXjVPNDgdmg=/?share_link_id=570223371949
