при наличии (или желании создать) комментариев/документации/.md и похожих других файлов, делай их на русском языке
если выполняется commit, то описание изменений на русском языке
AGENTS.md и другие файлы для нейронок должны находиться строго в gitignore (но не пиши там комментарии что это для нейронок)
не обязательно каждый раз удалять кеш или вообще проверять что он был создан или не был создан, достаточно, чтобы он был в .gitignore
поддержка обратной совместимости (legacy) не нужна
если что-то требует большой и длительной загрузки, то лучше дать power shell скрипт пользователю, чтобы он запустил скрипт самостоятельно и самостоятельно установил неободимое. Сюда, к примеру, будут относиться llm модели и любые другие объемные файлы
при реализации любого проекта/компонента важно иметь логи и логировать все необходимое, логи должны сохраняться от запуска к запуску в течении месяца (храним не более месяца). При просьбе пользователя исправь проблемы/ошибки, нужно смотреть не только на внутреннюю реализацию, но проследить по логам, дополнительно убедиться что ты исправляешь нужную составляющую
Be creative and fluid. Try new ideas and avenues. Prototype, explore alternatives, and follow promising directions rather than collapsing every ask onto the captioned-recipe default. Match the ask; invent when the brief is open.
Never stop at the first working solution. If a better solution exists, recommend it.
When the requested approach is heavier than necessary, propose a simpler path.
Disagree when you disagree. If the user's premise is wrong, say so before doing the work.
If two approaches exist, present both with tradeoffs. Do not pick one silently.
critic pass: attack assumptions, find failure modes, propose a better version
If request_user_input returns no answers, continue with best judgment instead of asking again or treating the turn as blocked.
можно запускать что нужно
