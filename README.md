# Change-shortstory-template
Переключатель шаблонов коротких новостей.

![Image alt](https://user-images.githubusercontent.com/44625352/54727544-ae10b500-4b78-11e9-850a-0f64054bdf9f.jpg)

---
 - Использует Cookie для хранения префикса шаблона.
 - Использует отдельные tpl шаблоны.
 - Работает с включённым кэшем.
 - Неограниченное создание шаблонов.
 - Ajax метод переключения.
---

## Установка
1. Установите плагин.
2. Создайте копии шаблона `shortstory.tpl` затем присвойте каждой копии имя в качестве суффикса shortstory_**suffix**.tpl.
3. В правках файла `init.php` найти массив с примером двумя шаблонами. По примеру дополните либо измените данные в массиве.
```php
$template_menu = array(
    'default'  => 'Стандарт',
    'thumb'    => 'Компактный'
);
```
>В качестве ключа указывается суффикс шаблона, а значение имя или html код. Разделяются запятыми (Подробней о [PHP:Array](https://www.php.net/manual/ru/language.types.array.php))

> Если в шаблоне оставить одно значение то переключатель не отображается.
> Такой же массив имеется в `engine/ajax/change_template.php`

4. В `main.tpl` или других шаблонах тегом **{change_short_template}** выводится переключатель.

## Дополнительная информация
1. Если нужно изменить HTML разметку, это делается в изменениях над файлов `engine/main.php`. В коде представленом ниже.
```php
$short_t_array = array();

foreach( $template_menu as $key => $val ) {

  	$active_template = ' ';
  	if( $user_tpl_tmp == $key ) $active_template = ' class="current" ';

  	$short_t_array[] = '<a href="#"'.$active_template.'data-template="'.$key.'">'.$val.'</a>';

}

$short_t_array = implode(' | ', $short_t_array);
$short_t = <<<HTML
   <div class="template-switcher">
        <div>{$short_t_array}</div>
   </div> 
HTML;
```

2. В файле `engine/ajax/change_template.php` есть переменная **$custom_navigation** в состоянии `true` пагинация добавляется отдельно если она размещалась без конкретного определения места тегом `{newsnavigation}`

2. Для изменения JavaScript кода переключателя делается в том же файле чуть ниже. В коде представленом ниже.
```php
$onload_scripts[] = <<<HTML
$(".template-switcher a[data-template]").click(function(){
        
	if( $(this).hasClass('current') ) return false;
	$(this).addClass('current').siblings().removeClass('current');
				
	let new_templ 	= $(this).data('template');
					
	setcookie('short_template', new_templ);
				
	ShowLoading('');
				
	$.post(dle_root + "engine/ajax/controller.php?mod=change_template", {short_template: new_templ, user_hash: dle_login_hash}, function(response) {
					
		if( response.success ) {

			//Объект куда вставлять разметку ответа.
			$('#content').find('.left-content > div').eq(0).html(response.returnbox);
			$('#content').find('.left-content > div').eq(0).append(response.navigation);

		} else {

			DLEAlert('Ошибка', 'Так-вот');

		}
						
		HideLoading('');

	}, 'json');        
		  
	return false;
});
HTML;
```
> Если объяснить коротко, то код ищет объект с конкретным атрибутом `id="content"` если есть то в нём же пытается найти дочерние объекты с атрибутом `class="left-content"` в котором находится `div`. В него то и будет помещена разметка из шаблонов кратких новостей которая содержится в переменной data.

> Строкой ниже определяется куда добавлять разметку пагинации. Если переменная `$custom_navigation` установлена в **true**. Если в **false** то строку стоит `закомментировать`.

> Функция `append` добавляет в самый конец.

> Подробней о выборке  [элементов](https://metanit.com/web/jquery/2.1.php)


