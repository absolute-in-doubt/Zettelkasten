
04-02-2026 08:55

Status:

Tags: [[CI & CD]]

---


![[Pasted image 20260204090748.png]]

### CI principles


Главная идея в том, чтобы разработчики периодически мерджили свои ветки в main. И этот merge триггерит CI tool to perform a “build” of the updated code base.

The CI system grabs the new code, compiles it with existing code and packages it with any dependencies, such as configuration files, libraries or other resources. 



### CD principles


Code builds that pass integration tests and validation stages are packaged and delivered to code repositories, which centralize and store code packages in a deployable state.

CD workflow тестирует и сам код и его зависимости. (Integration testing в имитации production среды).

The code is validated to help ensure that the software works across scenarios, and if it passes validation, the system notifies DevOps teams that the latest build is available. Team members have the opportunity to provide feedback about the new build and make any final suggestions for changes.

CD - не автоматический деплой в prod. Это доставка кода, готового для деплоя в место хранения (репозиторий). А DevOps команда вручную деплоит.

---
### References:

