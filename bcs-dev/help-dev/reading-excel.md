```js
Процедура КнопкаВыполнитьНажатие(Кнопка)
	пСтарт = ТекущаяДата();
	пСообщение = XMLСтрока(ТекущаяДата()) + ": Начинаю расчет количества файлов...";
	Сообщить(пСообщение);
	МассивФайлов = НайтиФайлы(Путь, "*.xlsx");
	пСчетчик = 0;
	пКоличествоФайлов = МассивФайлов.Количество();
	пСообщение = СтрШаблон("%1: Количество файлов составляет: %2", XMLСтрока(ТекущаяДата()), пКоличествоФайлов);
	Сообщить(пСообщение);
	Для Каждого пСтрока Из МассивФайлов Цикл
		ДвоичныеДанные = Новый ДвоичныеДанные(пСтрока.ПолноеИмя);
		пТЗ  = EXCELВТаблицуЗначений(ДвоичныеДанные, пСтрока.Расширение);
		Попытка
			пФинальнаяТаблица = ПреобразоватьФайл(пТЗ);
			Если пФинальнаяТаблица.Количество() <> 0 Тогда 
				//пПутьСохранения = "C:\Users\PelevinVA\Desktop\задача\" + пСтрока.Имя;
				пПутьСохранения = ПутьСохранения + "\" + пСтрока.Имя;
				
				пТипФайла = ТипФайлаТабличногоДокумента.XLSX;
				ПечатьТЗ(пФинальнаяТаблица, пПутьСохранения, пТипФайла);
			КонецЕсли;
		Исключение
			пСообщение = XMLСтрока(ТекущаяДата()) + ": Ошибка работы с файлом " + пСтрока.Имя + ОписаниеОшибки();
			Сообщить(пСообщение);
		КонецПопытки;
		пСчетчик = пСчетчик + 1;
		Если пСчетчик % 1 = 0 Тогда 
			пСообщение = СтрШаблон("%1: Обработано записей: %2 из %3", XMLСтрока(ТекущаяДата()),
                                              пСчетчик, пКоличествоФайлов);
			Сообщить(пСообщение);
		КонецЕсли;
		//Прервать; 
	КонецЦикла;
	пСообщение = СтрШаблон("%1: Время выполнения скрипта составляет: %2 s",
                              XMLСтрока(ТекущаяДата()), XMLСтрока(ТекущаяДата() - пСтарт));
	Сообщить(пСообщение);
КонецПроцедуры
```
```js
Функция EXCELВТаблицуЗначений(ДвоичныеДанные, Расширение)
	
	ФайлEXCELНаСервере = ПолучитьИмяВременногоФайла(Расширение);
	ДвоичныеДанные.Записать(ФайлEXCELНаСервере);
	
	ТабличныйДокумент = Новый ТабличныйДокумент;
	ТабличныйДокумент.Прочитать(ФайлEXCELНаСервере);
	УдалитьФайлы(ФайлEXCELНаСервере);
	ОбластьТаблицы = ТабличныйДокумент.Область();
	ПостроительЗапроса = Новый ПостроительЗапроса;
	ПостроительЗапроса.ИсточникДанных = Новый ОписаниеИсточникаДанных(ОбластьТаблицы);  
	ПостроительЗапроса.Выполнить();
	ТаблицаДанных = ПостроительЗапроса.Результат.Выгрузить();
	Для пСчетчик = 1 По 17 Цикл 
		ТаблицаДанных.Колонки.Удалить(2);
	КонецЦикла;
	
	Возврат ТаблицаДанных;
КонецФункции
```
```js
Функция ПреобразоватьФайл(пТЗ)
	//пТЗ = мРезультатЗапроса.Выгрузить();
	пТаблицаНесоответствий = Новый ТаблицаЗначений;
	пТаблицаНесоответствий.Колонки.Добавить("Соглашение");
	пТаблицаНесоответствий.Колонки.Добавить("ФИО");
	
	Для Каждого пСтрока Из пТЗ Цикл
		пФИО1 = СокрЛП(пСтрока.ФИО);
		//пФИО2 = СокрЛП(Лев(пСтрока.Соглашение, СтрНайти(пСтрока.Соглашение, "(") - 1));
		пФИО2 = СокрЛП(Лев(пСтрока.Соглашение, СтрДлина(пСтрока.ФИО)));
		//Сообщить(пФИО1);
		//Сообщить(пФИО2);
		Если пФИО1 <> пФИО2 Тогда
			пНоваяСтрокаТЧ = пТаблицаНесоответствий.Добавить();
			пНоваяСтрокаТЧ.Соглашение = пСтрока.Соглашение; 
			пНоваяСтрокаТЧ.ФИО = пСтрока.ФИО;
		КонецЕсли;
	КонецЦикла;
	//ПечатьТЗ(пТаблицаНесоответствий);
	
	Возврат пТаблицаНесоответствий; 
КонецФункции
```
```js
Процедура ПриОткрытии()
	Путь = "W:\ДВК Проверка ЦБ 2024\Заявка 19 п.14\30_06_2024";
КонецПроцедуры
```
