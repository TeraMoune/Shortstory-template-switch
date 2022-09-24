# Switch shortstory template
Ajax переключатель шаблонов коротких новостей.

DLE: 14>

![Image alt](https://user-images.githubusercontent.com/44625352/54727544-ae10b500-4b78-11e9-850a-0f64054bdf9f.jpg)

---
 - Использует Cookie для хранения префикса шаблона.
 - Использует отдельные tpl шаблоны.
 - Ajax метод переключения.
---

## Важно
Для полноценного использования требуются некоторые знания и понимание. Так как переключение шаблонов происходит посредством технологии Ajax то все бинды и события навешанные в момент открытия страницы будут сброшены и их потребуется инициализировать повторно. Всё это делается в JS коде описанном в Дополнительной информации ниже.

Еще могут возникнуть проблемы с различными модулями и плагинами которые применяли какие-то операции и функции вне файла `show.short.php`. Если таковые будут то разработкик этого плагина должен будет внести соответствующие правки в файл `change_template.php`.


## Установка
1. Установите `xml` плагин.
2. Создайте копии шаблона `shortstory.tpl` затем присвойте каждой копии имя в качестве суффикса shortstory_**suffix**.tpl.
3. Перечисление суффиксов шаблонов делается в переменной $template в виде ($suffix_template => text\icon). Переменная находится в первой правке файла functions.php в классе templateSwitcher. По умолчанию установлено два варианта один по умолчанию второй компактный стиль.

>В качестве ключа указывается суффикс шаблона, а значение имя или html код. Разделяются запятыми (Подробней о [PHP:Array](https://www.php.net/manual/ru/language.types.array.php))

> Если в шаблоне оставить одно значение то переключатель не отображается. Так же функции становятся неактивными.

4. В `main.tpl` или других шаблонах тегом **{switcherButton}** выводится переключатель. **{templates-class}** выводит суффикс шаблона для дополнительной вариативности стилей.

## Дополнительная информация
1. Если нужно изменить HTML разметку, это делается в изменениях над файлов `engine/modules/functions.php`. В функции `show_button` класса `templateSwitcher`.
2. В файле `engine/ajax/change_template.php` есть переменная **$custom_navigation** в состоянии `true` пагинация добавляется в отдельном ключе ответа json от сервера если она размещалась при помощи тега `{newsnavigation}`. В JS ниже нужно указать место для пагинации.

2. Для изменения JavaScript кода переключателя делается в правках файла `engine/modules/main.php`. В коде представленом ниже.

```php
$onload_scripts[] 	= <<<HTML
$(".template-switcher a[data-template]").click(function(){

	if( $(this).hasClass('current') ) return false;
	$(this).addClass('current').siblings().removeClass('current');

	let new_templ 		= $(this).data('template');
	let content_block 	= $('#content').find('.left-content > div').eq(0);
				
	setcookie('short_template', new_templ);

	ShowLoading();
            
	$.post(dle_root + "engine/ajax/controller.php?mod=change_template", {user_hash: dle_login_hash}, function(response) {
				
		if( response.success ) {

			content_block.html(response.returnbox);
			content_block.append(response.navigation);

		} else {

			DLEAlert('Ошибка', 'Так-вот');

		}

		HideLoading();

	}, 'json');        
      
	return false;
});
HTML;
```
> **let content_block 	= $('#content').find('.left-content > div').eq(0);** Если объяснить коротко, то код ищет объект с конкретным атрибутом `id="content"` если есть то в нём же пытается найти дочерние объекты с атрибутом `class="left-content"` в котором находится `div`. В него то и будет помещена разметка из шаблонов кратких новостей которая содержится в переменной data.

> Строкой ниже определяется куда добавлять разметку пагинации. Если переменная `$custom_navigation` установлена в **true**. Если в **false** то строку стоит `закомментировать`.

> Функция `append` добавляет в самый конец.

> Подробней о выборке  [элементов](https://metanit.com/web/jquery/2.1.php)

### Поддержать...
> ЮMoney: 4100115063692304
> 
> Qiwi nickname: TERAMOUNE
> 
> Wmz: Z990082286464

