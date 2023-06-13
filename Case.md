Название: **Кейсы**

Описание: **Получаем кейсы раз в сутки, покупаем кейсы, открываем кейсы с некоторым шансом лутаем кучу денег**

Версия: **1.1**

```Julia
{% set b = component.id %}
{% if b == null %}
{% do parameters.store('memberID', member.id) %}
{% else %}
{% require parameters.memberID returning '<:no:800777001190555658> '~'**Время использования команды истекло.**' %}
{% require parameters.memberID == member.id returning '<:no:800777001190555658> '~'**Вы не вызывали эту команду.**' %}
{% endif %}

{% set a = arguments.get(1) %}
{% set m = member.getAttribute('money') %}
{% set case = member.getAttribute('cases') %}
{% set cases = split(case,";") %}
{% set ccase = number_format((cases[0]), 0) %}
{% set rcase = number_format((cases[1]), 0) %}
{% set lcase = number_format((cases[2]), 0) %}
{% set count = number_format((cases[3]), 0) %}
{% global uuid = '5678340f-2de1-4360-aecd-577378e3b5d9' %} {# необходимо изменить #}
{# ========================= #}
{%- if (b == null and a == null) or b == 'home' %}
  > `🚩`** Цены кейсов: `5.000 | 10.000 | 50.000`**
  > `🧰`** Редкости кейсов:** `Common | Rare | Legendary`
	
  > `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
  > `📃`** Всего куплено:** `{{ number_format(count, 0, ',', '.') }} {{ plural(count, 'ru', 'штука', 'штуки', 'штук', 'штуки') }}`
  
  `!123 buy` ╏ `!123 free` ╏ `!123 open` {# необходимо изменить #}
  {% do button('SECONDARY', 'opencase', '', '<:restart:1117067797923631115>', uuid) %}
  {% do button('SECONDARY', 'casec', '', '<:case:1117148054999552020>', uuid) %}
  {% do button('SECONDARY', 'shop', '', '<:store:1117073106033463326>', uuid) %}

{%- elseif a == 'buy' or b == 'shop' %}

  `❓`** Какой кейс покупаем?**
  > `🚩`** Цены кейсов: `5.000 | 10.000 | 50.000`**
  > `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
  {% do button('SECONDARY', 5000, 'Common', '⬜', uuid) %}
  {% do button('SECONDARY', 10000, 'Rare', '🟦', uuid) %}
  {% do button('SECONDARY', 50000, 'Legendary', '🟥', uuid) %}

{% elseif number(b) %}
	{% if m < b %}
    > `💳`** Не достаточно средств на балансе!**
    `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
    `🚩`** Цена кейса:** `{{b}}`
  {% else %}
    {% do m.decrement(b) %}
    {% set new = "" %}
    {% if b > 10000 %}{% set new = ccase~";"~rcase~";"~lcase+1 %}{% elseif b > 5000 %}{% set new = ccase~";"~rcase+1~";"~lcase %}{% else %}{% set new = ccase+1~";"~rcase~";"~lcase %}{% endif %}
    {% do case.update(new~";"~count+1) %}
    > `✅`**На Ваш аккаунт был зачислен новый кейс**
    `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
    `⬜`** Всего  кейсов:** `{{ number_format((ccase+rcase+lcase+1), 0, ',', '.') }}`
  {% endif %}

{% elseif b == 'casec' %}
`{{ number_format((ccase), 0, ',', '.') }} {{ plural(ccase, 'ru', 'обычный', 'обычных', 'обычных', 'обычных') }}`
`{{ number_format((rcase), 0, ',', '.') }} {{ plural(rcase, 'ru', 'редкий', 'редких', 'редких', 'редких') }}`
`{{ number_format((lcase), 0, ',', '.') }} {{ plural(lcase, 'ru', 'легендарный', 'легендарных', 'легендарных', 'легендарных') }}`

{% elseif a == 'free' %}
{% set cd = member.getAttribute('casecooldown') %}
{% require cd.value < calendar('now').millis returning "`⏰` **След. Возможность: **" ~ format("`Через %s`", duration(cd.value - calendar('now'))) ~ format("\n**Точное время:**  `%s`", date(cd) ) %}
{% do cd.update(calendar('now').plusHours(24).millis) %}
{% set ccase = ccase+1 %}
{% do case.update(ccase~";"~rcase~";"~lcase~";"~count) %}
`✅`** Вы получили ежедневный `обычный` кейс**
> `⏰`** След. возможность:** `{{ format("Через %s", duration(cd.value - calendar('now'))) }}`
> `⬜`** Всего  кейсов:** `{{ number_format(ccase, 0, ',', '.') }} {{ plural((ccase+rcase+lcase), 'ru', 'штука', 'штуки', 'штук', 'штуки') }}`

{% elseif a == 'open' or b == 'opencase' %}

`❓`** Какой кейс открыть?**
> {{ number_format((ccase), 0, ',', '.') }} {{ plural(ccase, 'ru', 'обычный', 'обычных', 'обычных', 'обычных') }}
> {{ number_format((rcase), 0, ',', '.') }} {{ plural(rcase, 'ru', 'редкий', 'редких', 'редких', 'редких') }}
> {{ number_format((lcase), 0, ',', '.') }} {{ plural(lcase, 'ru', 'легендарный', 'легендарных', 'легендарных', 'легендарных') }}
{% do button('SECONDARY', 'com', 'Common', '⬜', uuid) %}
{% do button('SECONDARY', 'rar', 'Rare', '🟦', uuid) %}
{% do button('SECONDARY', 'leg', 'Legendary', '🟥', uuid) %}
{% elseif b == 'com' %}
  {% require ccase > 0 returning '`❌`** Кейс отсутствует.**' %}
  {% do case.update(ccase-1~";"~rcase~";"~lcase~";"~count) %}
	{% set loot = random(250, 10000) %}
  {% if loot > 5000 %}{% global shans = '`🟢`' %}{% elseif loot > 2500 %}{% global shans = '`🟡`' %}{% else %}{% global shans = '`🔴`' %}{% endif %}
	{% do m.increment(loot) %}
> **`📨` Дроп:** `{{ number_format(loot, 0, ',', '.') }}` :BCoinGIF:
> **`🎟` Шанс:** `{{ shans }}`
> `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
{% do button('SECONDARY', 'com', '', '<:case:1117148054999552020>', uuid) %}
{% elseif b == 'rar' %}
  {% require rcase > 0 returning '`❌`** Кейс отсутствует.**' %}
  {% do case.update(ccase~";"~rcase-1~";"~lcase~";"~count) %}
  {% set loot = random(1000, 15000) %}
  {% if loot > 10000 %}{% global shans = '`🟢`' %}{% elseif loot > 2500 %}{% global shans = '`🟡`' %}{% else %}{% global shans = '`🔴`' %}{% endif %}
	{% do m.increment(loot) %}
> **`📨` Дроп:** `{{ number_format(loot, 0, ',', '.') }}` :BCoinGIF:
> **`🎟` Шанс:** `{{ shans }}`
> `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
{% do button('SECONDARY', 'rar', '', '<:case:1117148054999552020>', uuid) %}
{% elseif b == 'leg' %}
  {% require lcase > 0 returning '`❌`** Кейс отсутствует.**' %}
  {% do case.update(ccase~";"~rcase~";"~lcase-1~";"~count) %}
  {% set loot = random(20000, 70000) %}
   {% if loot > 50000 %}{% global shans = '`🟢`' %}{% elseif loot > 35000 %}{% global shans = '`🟡`' %}{% else %}{% global shans = '`🔴`' %}{% endif %}
	{% do m.increment(loot) %}
> **`📨` Дроп:** `{{ number_format(loot, 0, ',', '.') }}` :BCoinGIF:
> **`🎟` Шанс:** `{{ shans }}`
> `💵`** Баланс:** `{{ number_format((m.value), 0, ',', '.') }}`
{% do button('SECONDARY', 'leg', '', '<:case:1117148054999552020>', uuid) %}
{%- endif %}
{% do button('DANGER', "home", '', ':arrow_left:', uuid) %}
```
