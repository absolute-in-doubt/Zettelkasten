
04-02-2026 08:52

Status:

Tags: [[CI & CD]]

---
# Окружения - CI&CD

~={orange}**Your project can be deployed into multiple environments.**=~

~={orange}**This means that you can have multiple “copies” of your project up and running at the same time, each having its own name and purpose.**=~


## Isolated Environments

- Поднимаются одним разрабом для теста какой-то фичи, после чего - уничтожаются.

![[Pasted image 20260204094837.png]]

Webiny project - просто проект (приложуха). Я просто с сайта Webiny взял картинку.


---
## Shared Environments

- Один раз задеплоили и всё. Always up.

Shared environment - shared between devs (dev, staging) or users (prod).

### Development 

Новая версия (из feature- или dev-ветки) попадает сразу после коммита или merge request — на этапе CI (build, unit/integration tests). Здесь быстрые проверки на работоспособность.

В Development environment integration tests проводятся на этапе CI для раннего выявления проблем в изолированных компонентах, но без полной имитации prod.

### Staging

После успешного CI на dev или merge в release/main — на этапе CD (integration/e2e tests, load testing), имитируя prod

Окружение имитирует production для финального тестирования релиз-кандидатов, включая end-to-end тесты, нагрузку и UAT (user acceptance testing). В CI/CD пайплайнах staging участвует в интеграционных тестах перед продвижением в prod, минимизируя риски.

> [!tip] **Почему нельзя интеграционные тесты проводить сразу в Staging?**
> Разделение ускоряет фидбек: dev для ежедневных итераций (дешевые, быстрые тесты), staging для финальной валидации без риска для пользователей. Тестировать сразу в staging неэффективно — частые коммиты (10+ в день) сломают его, замедлят разработку и усложнят отладку из-за конкуренции ресурсов.



### Production (Prod)

Это живое окружение для реальных пользователей, где акцент на стабильности и доступности. В CI оно задействовано в финальной стадии деплоя после всех проверок, часто с автоматизированным rollout (continuous deployment).



---
### References:

