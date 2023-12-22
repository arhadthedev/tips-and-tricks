Developing [[Our Browser]], we will follow the best practices of Chrome and Firefox by running any user-supplied interpreted code in a sandbox. A sandbox is a separate process with totally restricted access rights so no operation on OS securable objects is available but reading to and writing from already existing pipes.

## Windows

- build with Address Space layout Randimization (ASLR)
- build with Data Execution Prevention (DEP)
- build with Control Flow Guard (CFG)
- build as a console application to prevent `win32k.sys` subsystem creation
- do not import from `user32.dll` and `gdi32.dll` for the same reason
- [`SetProcessMitigationPolicy()`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-setprocessmitigationpolicy) (Windows 8): limit process interaction with the system
	- `ProcessDynamicCodePolicy
	- `ProcessStrictHandleCheckPolicy`
	- `ProcessSystemCallDisablePolicy`
	- `ProcessSignaturePolicy` и `ProcessImageLoadPolicy`
	- `ProcessExtensionPointDisablePolicy`
	- `ProcessFontDisablePolicy`
	- `ProcessSideChannelIsolationPolicy` (Windows 10)
	- `ProcessUserShadowStackPolicy` (Windows 10)

### Behind the scenes

System call details from https://stackoverflow.com/questions/53983079/trying-to-understand-process-mitigation-policies-that-can-be-set-by-setprocessmi#comment94846156_53983079 (by Neitsa, the citation is licensed under CC-BY-SA 4.0):

> for `DisallowWin32kSystemCalls` the filtering occurs **only** for syscalls that are directed towards win32k.sys. There are two types of syscalls in windows: the ones that are made to the kernel, or the ones that are directed to the windowing subsystem (in this case the 12th bit of the syscall number is set to 1)

Process-to-GDI interaction details from https://chat.stackoverflow.com/transcript/message/44968843#44968843 (by Neitsa, the citation is licensed under CC-BY-SA 4.0):

> When user32.dll or gdi32.dll is loaded, a call is made to the kernel to switch the current thread to a special thread (called a GUI thread, it has some special structures, a special stack and some special flags in the kernel), but basically it just tells "that process is a GUI process from now on".
> 
> The `SetProcessMitigationPolicy` function doesn't know if the process is a GUI one or not, this is checked when you actually do a syscall. On the other hand, you cannot set the mitigation once the process is already a GUI process (it's too late), this check is done in the `NtSetInformationProcess` function (where `SetProcessMitigationPolicy`is just a wrapper around this function).

On `ProcessFontDisablePolicy` dscribed as "*turns off the ability of the process to load non-system fonts*:

> It probably refers to fonts not installed by Explorer, but fonts added with `AddFontResource`.
## Linux

- [seccomp](https://en.wikipedia.org/wiki/Seccomp)