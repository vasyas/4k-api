# Актуальная версия документи в Confluence 4Конверта !! #


# Обзор #

API сервиса www.4konverta.com построено на основе принципов REST. На этой страничке я привел адреса страниц API и какие операции с ними возможны.

В общем, в ответе на POST и GET запросы возвращается документ в формате XML. Данные на сервер передаются в виде параметров в POST-запросах. Для удаления объета используется запрос DELETE.

При необходимости, некоторые символы в XML могут быть заменены на XML-сущности для квотирования (например, `<`actualExpression`>`12 пиво &quot;Янтарь&quot;`<`/actualExpression`>`)

Считается, что все входные данные синтаксически верные. В случае ошибки сервер возвращает код статуса 500, тело ответа при этом неопределенное.

Комментарии и вопрос писать в форуме "4-х конвертов" по адресу
http://www.4konverta.com/forum/forums/show/10.page

В описании в фигурных скобках описаны параметры, которые нужно подставлять в адреса.
Пример параметров:
| user | Логин пользователя |
|:-----|:------------------------------------|
| date | Дата в формате yyyy-MM-dd |
| personId | Идентификатор члена семьи, возвращается в /data/{user} |
| envelopeBegin | Дата начала недели. Если на указанной дате неделя не начинается, буде выбран конверт, в который входит указанная дата. |

<br>
<h1>История</h1>

Текущая ревизия API - <b>1.2</b> (в процессе разработки)<br>
<br>
- Добавлено управления счетами <br>
- Убраны фактические накопления на цель <br>
- Добавлено распределение остатка <br>
- Управление целями (в процессе) <br>

<a href='http://code.google.com/p/4k-api/source/browse/wiki/ApiDescription.wiki?spec=svn49&r=49'>Версия 1.1</a>


- Добавлена поддержка счетов<br>
<br>
<a href='http://code.google.com/p/4k-api/source/browse/wiki/ApiDescription.wiki?spec=svn39&r=39'>Версия 1.0</a>



<br>
<h1>Авторизация</h1>

Для авторизации с каждым запросом нужно передавать два заголовка.<br>
<table><thead><th> 4KApplication </th><th> Идентификатор стороннего приложения. <br> Раздается авторами сервиса "4 конверта" <br> Для тестов можно использовать значение Demo. При использовании Demo использование API ограничено 25 различными логинами в сутки (для всех пользователей) </th></thead><tbody>
<tr><td> 4KAuth </td><td> Пароль пользователя </td></tr></tbody></table>

Если авторизация неверна, будет возращен код ошибки 401. После неверных 3-х попыток логина, учетная запись пользователя блокируется. Чтобы её разблокировать, нужно ввести логин/пароль на сайте, указав при этом капчу.<br>
<br>
<br>
<h1>Версионность</h1>

Для указания версии используется заголовок 4KVersion. Клиент может опускать этот заголовок, в таком случае будет использована версия 1.0. Формат версии - число с плавающей точкой.<br>
<br>
<br>
<h1>Описание API</h1>
<h3>1. Общие функции</h3>

<b>1.1. Получение информации о пользователе</b> <br>
URL: /data/{user} <br>
Доступные действия: GET <br>
Результат:<br>
<pre><code>&lt;user&gt;<br>
  &lt;country code="gb"&gt;Великобритания&lt;/country&gt;<br>
  &lt;currency id="gb" code="GBP"&gt;фунт&lt;/currency&gt; &lt;!-- Основная валюта --&gt;<br>
  &lt;firstDayOfWeek&gt;1&lt;/firstDayOfWeek&gt; &lt;!-- 1 - sunday, 2 - monday --&gt;<br>
  &lt;timeZone&gt;Europe/London&lt;/timeZone&gt;<br>
  &lt;disableExtendedSyntax&gt;true&lt;disableExtendedSyntax&gt; &lt;!-- Отключить обработку слова "по" в описании тратах --&gt;<br>
<br>
  &lt;persons&gt;<br>
    &lt;person id="23443234" name="Петя"/&gt;<br>
    &lt;person&gt;...&lt;/person&gt;<br>
  &lt;/persons&gt;<br>
<br>
  &lt;accounts&gt;<br>
    &lt;account id="441232" name="Наличка" archived="false"&gt;<br>
      &lt;currency id="gb" code="GBP"&gt;фунт&lt;/currency&gt;<br>
      &lt;value&gt;250.0&lt;/value&gt;<br>
    &lt;/account&gt;<br>
    ...<br>
  &lt;/accounts&gt;<br>
&lt;/user&gt;<br>
</code></pre>

<b>Получение списка доступных валют</b> <br>
URL: /data/{user}/currencies <br>
Доступные действия: GET <br>
Результат:<br>
<pre><code>&lt;currencies&gt;<br>
  &lt;currency id="ru" code="RUB" rate="43.2"&gt;фунт&lt;/currency&gt;<br>
&lt;/user&gt;<br>
</code></pre>

<b>1.2. Работа со счетами</b> <br>
URL: /data/{user}/account/{accountId} <br>
Доступные действия: GET,POST,DELETE <br>
Результат:<br>
<pre><code>&lt;account id="441232" name="Наличка" archived="true"&gt;<br>
  &lt;currency id="gb" code="GBP"&gt;фунт&lt;/currency&gt;<br>
  &lt;value&gt;250.0&lt;/value&gt;<br>
&lt;/account&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> name </th><th> Название счета </th></thead><tbody>
<tr><td> archived </td><td> true/false Должен ли счет быть видимым </td></tr></tbody></table>

При создании нового счета нужно передавать в качестве id строку new. При этом вместе с предыдущими доступны такие параметры<br>
<table><thead><th> currency </th><th> идентификатор валюты из списка допустимых </th></thead><tbody>
<tr><td> value </td><td> начальный баланс в валюте счета. На эту сумму будет увеличен остаток </td></tr></tbody></table>

<br><br>
<b>Управление переводами между счетами</b> <br>
Каждый перевод между счетами записывается как две операции изменения счетам (AccountChange). <br>
При создании (удалении) одного изменения второе создается (удаляется) автоматически. <br>
Суммы, на которые изменяются счета, не зависит одна от другой (это сделано для поддержки обменных курсов). <br>
URL: /data/{user}/accountChange/{accountChangeId} <br>
Доступные действия: GET,POST,DELETE. <br>
Редактирование переводов не предусмотрено. <br>
Результат:<br>
<pre><code>&lt;accountChange id="3423467" accountId="441232"&gt;<br>
  &lt;change&gt;250.0&lt;/change&gt; &lt;!-- баланс счета увеличивается на эту величину --&gt;<br>
  &lt;date&gt;2009-05-20&lt;/date&gt;<br>
  &lt;comment&gt;Снял наличку с депо&lt;/comment&gt;<br>
  <br>
  &lt;matchingAccountChange id="544523" accountId="321233"&gt; &lt;!-- Ссылка на изменения счета-корреспондента --&gt;<br>
    &lt;change&gt;-250.0&lt;/change&gt; &lt;!-- баланс счета-корреспондента увеличивается на эту величину --&gt;<br>
  &lt;/matchingAccountChange&gt; <br>
 &lt;/account&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> accountId </th><th> Счет 1 </th></thead><tbody>
<tr><td> change </td><td> Изменение счета 1 </td></tr>
<tr><td> matchingAccountId </td><td> Счет 2 </td></tr>
<tr><td> matchingChange </td><td> Изменения счета 2. Обычно, если change < 0, то matchingChange > 0, и наооборот </td></tr>
<tr><td> date </td><td> Дата перевода </td></tr>
<tr><td> comment </td><td> Примечание к переводу </td></tr></tbody></table>


<b>1.3. Работа с целями</b> <br>
<b>Получение списка категорий целей</b> <br>
URL: /data/{user}/goalCategories/ <br>
Доступные действия: GET <br>
<pre><code>&lt;goalCategories&gt;<br>
  &lt;goalCategory id="1" bold="true" order="1"&gt;Автомобиль&lt;/goalCategory&gt;<br>
&lt;/goalCategories&gt;<br>
</code></pre>

<b>Получение списка целей</b> <br>
URL: /data/{user}/goals/ <br>
Доступные действия: GET <br>
<pre><code>&lt;goals&gt;<br>
  &lt;goal id="2332" name="Лыжи" category="Отдых" categoryId="1" default="false" archived="false"&gt; &lt;!-- default=true для резервного фонда --&gt;<br>
    .. &lt;!-- Тело такое же, как и при управлении целью --&gt;<br>
  &lt;/goal&gt;<br>
&lt;/goals&gt;<br>
</code></pre>

<b>Управление целью</b> <br>
URL: /data/{user}/goal/{goalId} <br>
Доступные действия: GET, POST, DELETE <br>
<pre><code>&lt;goal id="2332" name="Лыжи" category="Отдых" categoryId="1" default="false" archived="false"&gt; &lt;!-- default=true для резервного фонда --&gt;<br>
  &lt;currency id="gb" code="GBP"&gt;фунт&lt;/currency&gt;<br>
  &lt;price&gt;250.0&lt;/price&gt; <br>
  &lt;created&gt;2009-01-12&lt;/created&gt; <br>
  &lt;planned&gt;2011-12-01&lt;/planned&gt; <br>
  <br>
  &lt;credit&gt;<br>
    &lt;value&gt;100.0&lt;/value&gt;<br>
    &lt;period&gt;WEEKLY&lt;/period&gt;<br>
  &lt;/credit&gt;<br>
  <br>
  &lt;accumulated&gt;125.0&lt;/accumulated&gt;<br>
  &lt;withdrawn&gt;100.0&lt;/withdrawn&gt; &lt;!-- Сколько средств потрачено из накопленных на цель --&gt;<br>
&lt;/goal&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> name </th><th> Название цели </th></thead><tbody>
<tr><td> categoryId </td><td> Id категории цели </td></tr>
<tr><td> price </td><td> Стоимость цели </td></tr>
<tr><td> created </td><td> Дата начала накопления </td></tr>
<tr><td> planned </td><td> Планируемая дата достижения </td></tr>
<tr><td> creditValue </td><td> Размер откладываемой на цель суммы </td></tr>
<tr><td> creditPeriod </td><td> Периодичность зачисления. Возможные значения NONE,WEEKLY,BIWEEKLY,MONTHLY,BIMONTHLY,QUARTERLY </td></tr></tbody></table>

Для новосоздаваемых целей также можно указать<br>
<table><thead><th> currency </th><th> Валюта цели </th></thead><tbody>
<tr><td> accumulated </td><td> Сколько уже накоплено на цель. При создании цели на эту сумму будет уменьшет остаток. </td></tr></tbody></table>

<b>Перечень списаний с цели <i>не реализовано</i></b> <br>
URL: /data/{user}/goal/{goalId}/withdrawals<br>
<pre><code>&lt;goalWithdrawals&gt;<br>
  &lt;goalWithdrawal id="4313"&gt;<br>
    &lt;goal id="2332" name="Машина" category="Покупка" default="false" archived="false"/&gt;<br>
    &lt;date&gt;2012-01-15&lt;/date&gt;<br>
    &lt;comment&gt;Аванс за машину&lt;/comment&gt;<br>
    &lt;account id="441232" name="Наличка"/&gt;<br>
    &lt;value&gt;150000.0&lt;/value&gt;<br>
  &lt;/goalWithdrawal&gt;<br>
&lt;/goalWithdrawals&gt;<br>
</code></pre>

<b>Управление списаниями с цели</b> <br>
URL: /data/{user}/goalWithdrawal/{goalWithdrawalId}<br>
Доступные действия: GET, POST, DELETE <br>
<pre><code>&lt;goalWithdrawal id="4313"&gt;<br>
  &lt;goal id="2332" name="Машина" category="Покупка" default="false"/&gt;<br>
  &lt;date&gt;2012-01-15&lt;/date&gt;<br>
  &lt;comment&gt;Аванс за машину&lt;/comment&gt;<br>
  &lt;account id="441232" name="Наличка"/&gt;<br>
  &lt;value&gt;150000.0&lt;/value&gt;<br>
&lt;/goalWithdrawal&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> date </th><th> Дата списания </th></thead><tbody>
<tr><td> comment </td><td> Примечание </td></tr>
<tr><td> value </td><td> Размер списания </td></tr></tbody></table>

При создании списания также указывается<br>
<table><thead><th> goalId </th><th> Идентификатор цели </th></thead><tbody>
<tr><td> accountId </td><td> Счет, с которого снимаются деньги </td></tr></tbody></table>

<b>Перечень переносов с цели/на цель</b> <br>
URL: /data/{user}/goal/{goalId}/updates <br>
Доступные действия: GET <br>
<pre><code>&lt;goalUpdates&gt;<br>
  &lt;goalUpdate id="213" type="goal"&gt;<br>
    .. &lt;!-- Тело как при управлении переносами  --&gt;<br>
  &lt;/goalUpdate&gt;<br>
&lt;/goalUpdates&gt;<br>
</code></pre>

<b>Управление переносами с цели/на цель</b> <br>
URL: /data/{user}/goalUpdate/{goalUpdateId} <br>
Доступные действия: GET, POST, DELETE <br>
У переноса может быть один из следующих типов <br>
<table><thead><th> GoalTransfer </th><th> Перенос средств между целями </th></thead><tbody>
<tr><td> RemainingTransfer </td><td> Перенос средств между целью и остатком </td></tr>
<tr><td> Update </td><td> Начальное задание суммы, накопленной на цель. Создается при выравнивании баланса </td></tr></tbody></table>

Результат:<br>
<pre><code>&lt;goalUpdate id="213" type="GoalTransfer"&gt;<br>
  &lt;goal id="2332" name="Машина" category="Покупка" default="false"/&gt;<br>
  &lt;date&gt;2012-05-12&lt;/date&gt;<br>
  &lt;value&gt;150.0&lt;/value&gt; &lt;!-- На эту величину увеличиваются накопленные на цель средства --&gt;<br>
  &lt;comment&gt;Купил машину дешевле, разницу в резервный фонд&lt;/comment&gt;<br>
  <br>
  &lt;account id="441232" name="Наличка"/&gt; &lt;!-- Только для type = Update --&gt;<br>
  &lt;matchingGoalUpdate id="223" type=""/&gt; &lt;!-- Только для type = GoalTransfer --&gt;<br>
&lt;/goalUpdate&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> date </th><th> Дата списания </th></thead><tbody>
<tr><td> value </td><td> Размер списания </td></tr>
<tr><td> comment </td><td> Примечание </td></tr></tbody></table>

При создании переноса также указывается<br>
<table><thead><th> goalId </th><th> Идентификатор цели, с которой переносяться средства </th></thead><tbody>
<tr><td> matchingGoalId </td><td> Идентификатор цели, на которую переносяться средства, или 'remaining' если переносяться в остаток </td></tr></tbody></table>

<h3>2. Ежедневные траты из конвертов</h3>

<b>2.1. Информация о недельном конверте</b> <br>
URL: /data/{user}/envelope/{envelopeBegin} <br>
Доступные действия: GET <br>
Результат:<br>
<pre><code>&lt;envelope begin="2009-05-18" size="1005.0"&gt;<br>
  ...<br>
  &lt;person id="23443234" name="Петя"&gt;<br>
    &lt;dailyExpense date="2009-05-20" defaultAccount="441232"&gt;<br>
      &lt;!-- Если нет ни одного expression, трата не введена --&gt;<br>
      &lt;expression account="441232" currency="gb"&gt;150 кафе, 20 еда&lt;/expression&gt; &lt;!-- В качестве account может быть любой счет в нац.валюте --&gt;<br>
      &lt;expression account="21442" currency="gb"&gt;30 интернет&lt;/expression&gt;<br>
      &lt;sum&gt;170.0&lt;/sum&gt;<br>
    &lt;/dailyExpense&gt;<br>
    ...<br>
  &lt;/person&gt;<br>
  ...<br>
&lt;/envelope&gt;<br>
</code></pre>

<b>2.2. Получение и внесение ежедневных трат</b> <br>
URL: /data/{user}/dailyExpense/{personId}/{date} <br>
Доступные действия: GET, POST <br>
Результат:<br>
<pre><code>&lt;dailyExpense date="2009-05-20" defaultAccount="441232"&gt;<br>
  &lt;!-- Если нет ни одного expression, трата не введена --&gt;<br>
  &lt;expression account="441232" currency="gb"&gt;150 кафе, 20 еда&lt;/expression&gt; <br>
  &lt;expression account="21442" currency="ru"&gt;30 интернет&lt;/expression&gt;<br>
  &lt;sum&gt;170.0&lt;/sum&gt;<br>
&lt;/dailyExpense&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> expression </th><th> выражения для траты, напр. 150 кафе, еда </th></thead><tbody>
<tr><td> account </td><td> id счета, 21442 </td></tr>
<tr><td> currency </td><td> идентификатор валюты, gb </td></tr></tbody></table>

<b>2.3. Распределение остатка недельного конверта</b> <br>
URL: /data/{user}/envelopeResult/{envelopeBegin}/ <br>
Доступные действия: GET, POST <br>
Результат:<br>
<pre><code>&lt;envelopeResult&gt;<br>
  &lt;value&gt;150.0&lt;/value&gt; &lt;!-- Сколько осталось из указанного конверта, может быть со значком минус --&gt;<br>
  &lt;distributeTo&gt;remaining&lt;/distributeTo&gt; &lt;!-- Куда ложить остаток, м.б. remaining или nextEnvelope --&gt;<br>
  &lt;distributed&gt;true&lt;/distributed&gt; &lt;!-- При ответе пользователе (первом вызове POST) становится false --&gt;<br>
&lt;/envelopeResult&gt;<br>
</code></pre>
Параметры POST:<br>
<table><thead><th> distributeTo </th><th> Куда ложить остаток конверта </th></thead><tbody></tbody></table>

<h3>3. Выполнение плана трат и доходов</h3>

<b>3.1. Перечень пунктов для выполнения на выбранной неделе</b> <br>
URL: /data/{user}/execution/{envelopeBegin} <br>
Доступные действия: GET <br>
Результат:<br>
<pre><code>&lt;execution begin="2009-05-18" remaining="950.0"&gt; &lt;!-- Текущий остаток, не зависит от начала недели --&gt;<br>
  &lt;!-- Планируемая и фактическая дата получаемого дохода --&gt;<br>
  &lt;actualIncome actualDate="2009-05-18" plannedDate="2009-05-18" defaultAccount="441232"&gt;<br>
    &lt;income id="3341123" name="Зарплата"&gt;<br>
      &lt;currency code="RUR" id="ru"&gt;руб&lt;/currency&gt;<br>
    &lt;/income&gt;<br>
    &lt;planned&gt;16000.0&lt;/planned&gt;<br>
    &lt;actual&gt;15500.0&lt;/actual&gt; &lt;!-- Сумма всех actualExpression --&gt;<br>
    &lt;!-- Если нет ни одного actualExpression, трата не введена --&gt;<br>
    &lt;actualExpression account="441232" currency="gb"&gt;аванс 8000 + зарплата 7500&lt;actualExpression&gt;<br>
    &lt;actualExpression account="3323 currency="ru"&gt;323&lt;/actualExpression&gt;<br>
  &lt;/actualIncome&gt;<br>
  &lt;actualExpense actualDate="2009-05-18" plannedDate="2009-05-18" defaultAccount="441232"&gt;<br>
    &lt;expense id="44324553" name="Арендная плата"&gt;<br>
      &lt;currency code="RUR" id="ru"&gt;руб&lt;/currency&gt;<br>
    &lt;/expense&gt;<br>
    &lt;planned&gt;3000&lt;/planned&gt;<br>
    &lt;actual&gt;&lt;/actual&gt;<br>
  &lt;/actualExpense&gt;<br>
  ...<br>
  &lt;envelope begin="2009-05-18" size="1005.0"&gt; &lt;!-- Такой же, как в п.2.1 --&gt;<br>
    ...<br>
  &lt;/envelope&gt;<br>
&lt;/execution&gt;<br>
</code></pre>

<b>3.2. Получение дохода</b> <br>
URL: /data/{user}/actualIncome/{incomeId}/{plannedDate} <br>
Доступные действия: GET, POST <br>
Результат:<br>
<pre><code>&lt;actualIncome actualDate="2009-05-18" plannedDate="2009-05-18" defaultAccount="441232"&gt;<br>
  &lt;income id="3341123" name="Зарплата"&gt;<br>
    &lt;currency code="RUR" id="ru"&gt;руб&lt;/currency&gt;<br>
  &lt;/income&gt;<br>
  &lt;planned&gt;16000.0&lt;/planned&gt;<br>
  &lt;actual&gt;15500.0&lt;/actual&gt;<br>
  &lt;actualExpression account="2212" currency="ru"&gt;15500&lt;/actualExpression&gt;<br>
&lt;/actualIncome&gt;<br>
</code></pre>
Параметры POST:<br>
<pre><code>value=1500.0&amp;account=2123&amp;currency=ru<br>
</code></pre>
или<br>
<pre><code>date=2009-05-20<br>
</code></pre>

<b>3.3. Выполнение траты</b> <br>
URL: /data/{user}/actualExpense/{expenseId}/{plannedDate} <br>
Доступные действия: GET, POST <br>
Результат:<br>
<pre><code>&lt;actualExpense actualDate="2009-05-18" plannedDate="2009-05-18" defaultAccount="441232"&gt;<br>
  &lt;expense id="44324553" name="Арендная плата"&gt;<br>
    &lt;currency code="RUR" id="ru"&gt;руб&lt;/currency&gt;<br>
  &lt;/expense&gt;<br>
  &lt;planned&gt;3000&lt;/planned&gt;<br>
  &lt;actual&gt;&lt;/actual&gt;<br>
&lt;/actualExpense&gt;<br>
</code></pre>
Параметры POST:<br>
<pre><code>value=1500.0&amp;account=2123&amp;currency=ru<br>
</code></pre>
или<br>
<pre><code>date=2009-05-20<br>
</code></pre>