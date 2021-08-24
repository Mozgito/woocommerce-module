# User roles

С версии `4.3.7` опция `"Роли клиентов"` была удалена из настроек модуля. Сейчас выгружаются пользователи со всеми доступными ролями в CMS.
Добавили фильтр `retailcrm_customer_roles` для корректировки выгружаемых пользователей.

## Пример работы фильтра
В приведенном ниже примере показано, как возможно корректировать роли выгружаемых пользователей:

```php
<?php

add_filter('retailcrm_customer_roles', 'editCustomerRoles', 10, 1);

function editCustomerRoles($roles)
{
    if (isset($roles['customer'])) {
        unset($roles['customer']);
    }
    
    return $roles;
}
```

## Описание работы функционала
У каждого зарегистрированного пользователя в WP есть роль. Роль отображает права пользователя на сайте. Ниже приведены основные кейсы работы с пользователями:

* Пользователь "гость (клиент без регистрации)" создает заказ. Заказ корректно выгрузился в CRM, клиент был создан без externalId. Если этот "гость" создаст еще один заказ, то заказ корректно выгрузится и установится связь с этим клиентом (будет произведен поиск по email).
* Создали пользователя, например с ролью "Подписчик", он корректно выгрузился в CRM, есть externalId и данные по клиенту, только те, что указаны при создании в админке WP. Данный пользователь оформляет заказ, заказ корректно выгрузился в CRM и связался с клиентом, клиенту добавилась данные: телефон, адрес. Обновление данных пользователя происходит корректно (будет произведен поиск по externalId).
* Функционал корректно работает и для корпоративных клиентов.
* Запустили архивную выгрузку, данные выгрузились корректно, все заказы, все клиенты без дублей.
* Запретили через фильтр передавать пользователей с ролью "Клиент", такие пользователи в CRM выгружаться не будут.
* Создали заказ для пользователя с ролью "Клиент", заказ выгрузился в CRM, так же создался клиент без externalId, модуль воспринимает таких клиентов как "гостей". Данное поведение считается корректным тк мы не выгружаем пользователей с определенной ролью, но они создаются с заказов (заказы мы передаем). 
