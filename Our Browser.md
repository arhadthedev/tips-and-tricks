A [[Desktop Application]] that resolves a user pain of:

- slow startup
- slow working
- irreversible state reset on background tab unload
- increased swap file consumption

Resolution of there pains woud help programs that integrate the engine.

Storage format:
- [CSSOM](https://habr.com/ru/companies/oleg-bunin/articles/660687/)
	- store bytecode, not just values

Performance:
- [Как достичь производительного рендеринга в браузере](https://habr.com/ru/company/oleg-bunin/blog/660687/)- our VisualModel should be renamed into a render tree, we have paint but no Layout, composite moves into the VisualModel