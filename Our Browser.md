A [[Desktop Application]] that resolves a user pain of:

- slow startup
- slow working
- irreversible state reset on background tab unload
- increased swap file consumption

Resolution of there pains woud help programs that integrate the engine.

Storage format:
- [CSSOM](https://habr.com/ru/companies/oleg-bunin/articles/660687/)
	- store bytecode, not just values

GUI style (glass-based): [ASMR Programming - Profile Card UI Design - No Talking](https://www.youtube.com/watch?v=kkS2ess19es)

Performance:
- [Как достичь производительного рендеринга в браузере](https://habr.com/ru/company/oleg-bunin/blog/660687/)- our VisualModel should be renamed into a render tree, we have paint but no Layout, composite moves into the VisualModel

Known issues:
- https://habr.com/ru/company/tinkoff/blog/722708/: Бордер рябит на десктопе при изменении масштаба. Мы не можем быть уверены, что у всех клиентов на компьютерах стоит масштаб 100%

[Как мы делали редизайн писем, почему это было так сложно и почему лучшее письмо — самое простое]()

Hard link creation to duplicate cache entries in a tab directory without copying:

```cpp
CreateHardLink("C:\\0\\hl.dat", "C:\\0\\1.cpp", NULL)
```

Unplivileged user impersonation:

```cpp
#include <windows.h>

static HANDLE ConvertToRealHandle(HANDLE h,
						 BOOL bInheritHandle)
{
	HANDLE hConverted = NULL;
 DuplicateHandle(GetCurrentProcess(), h,
						GetCurrentProcess(), &hConverted,
						0, bInheritHandle, DUPLICATE_SAME_ACCESS);
 return hConverted;
}

int main()
{
	//HANDLE hThread = OpenThread(THREAD_ALL_ACCESS, FALSE, GetCurrentThreadId());
	//DWORD a = GetLastError();

	const HANDLE hThread = ConvertToRealHandle(GetCurrentThread(), FALSE);

	const BOOL bInpersonalized = ImpersonateAnonymousToken(hThread);
	const DWORD dwE = GetLastError();

	STARTUPINFOW si;
	si.cb = sizeof(si);
	si.lpReserved = NULL;
	si.lpDesktop = NULL;
	si.lpTitle = NULL;
	si.dwFlags = 0;
	si.cbReserved2 = 0;
	si.lpReserved2 = NULL;
	PROCESS_INFORMATION pi;
	const BOOL b = CreateProcessW(
		/* lpApplicationName   */ L"C:\\1\\notepad.exe",
		/* lpCommandLine       */ L"",
		/* lpProcessAttributes */ NULL,
		/* lpThreadAttributes  */ NULL,
		/* bInheritHandles     */ FALSE,
		/* dwCreationFlags     */ 0,
		/* lpEnvironment       */ NULL,
		/* lpCurrentDirectory  */ NULL,
		/* lpStartupInfo       */ &si,
		/* lpProcessInformation*/ &pi
	);
	const DWORD dwEE = GetLastError();

	RevertToSelf();

	WaitForSingleObject(pi.hProcess, INFINITE);
	CloseHandle(pi.hThread);
	CloseHandle(pi.hProcess);
	CloseHandle(hThread);
}
```

## BOOKMARKS.DAT Format

Essentially, it's an HTML DOM with UL, LI and A.

\[HTML+head+body is a block with no extra numbering attributes\]

Body block IDs [1/2 bytes - depends on how many blocks are allowed inside BODY]:
- CDATA = 0
- UL = 1
- LI = 2
- A = 3

CDATA: the rest of a linked list chain is deflated content of UTF-8 text.

##  FontForge Font Editor

Site: http://designwithfontforge.com/en-US/index.html

Manual: http://designwithfontforge.com/en-US/index.html

Discovered about this program in *[В подключенном шрифте не показывается конкретный символ](https://ru.stackoverflow.com/questions/790358)*:

> UPD: [Если можно удалить символ](https://ru.stackoverflow.com/questions/535148/%D0%9A%D0%B0%D0%BA-%D1%83%D0%B4%D0%B0%D0%BB%D0%B8%D1%82%D1%8C-%D0%BD%D0%B5%D0%BD%D1%83%D0%B6%D0%BD%D1%8B%D0%B5-%D1%81%D0%B8%D0%BC%D0%B2%D0%BE%D0%BB%D1%8B-%D0%B2-%D1%88%D1%80%D0%B8%D1%84%D1%82%D0%B5-%D1%84%D0%BE%D1%80%D0%BC%D0%B0%D1%82%D0%B0-ttf?rq=1) с помощью FontForge, то получается можно и добавить, но где и как в таблице найти неразрывный пробел?

# Compression

[Как развитие алгоритмов сжатия остановилось 20 лет назад, или о новом конкурсе на 200 тысяч евро](https://habr.com/ru/articles/570694/) - arithmetic compression, Asymmetric numeral systems.