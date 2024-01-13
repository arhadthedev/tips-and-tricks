A script engine for [[Our Browser]].

## Dutyblasm source code tricks

`src/abi_helpers.cpp`:

```cpp
/** abi_helpers.cpp - suggestions on interaction of hosts and bytecode modules
 *
 * Copyright (c) 2022 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

#include <dutyblasm/abi_helpers.hpp>

dutyblasm::host_abi::syscall_dispatcher::syscall_dispatcher(
	std::span<bool (*)(std::span<value>)> syscalls) noexcept
	: syscalls(syscalls)
{
}

dutyblasm::block_values
dutyblasm::host_abi::run_nonstop(std::span<bool (*)(std::span<value>)> syscalls,
                                 const module &bytecode,
                                 block_values &&arguments,
                                 ) noexcept;
{
}
```

`src/bytecode_parser.cpp`:

```cpp
/** bytecode_parser.cpp - implementation of a base class for bytecode parsers
 *
 * Copyright (c) 2022 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */
#include <dutyblasm/bytecode.hpp>
#include <dutyblasm/utility.hpp>
#include <utility>

template<std::integral... types>
static std::optional<std::tuple<types...>>
dutyblasm::parse(std::span<const std::byte> buffer)
{
	const std::size_t total_size = 0 + ... + sizeof(types);
	if (total_size > buffer.size()) {
		return std::nullopt;
	}
	auto copy = []<std::integral type, std::size_t offset>(auto buffer) {
		return *reinterpret_cast<const type *>(buffer.data() + offset);
	}
	const auto offsets = sum<sizeofs<types>>;
	return [copy, buffer]<std::size_t... i>(std::index_sequence<i...>) {
		return {copy<std::get<i>(types), std::get<i>(offsets)>(buffer)...};
ssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssss
	}(std::index_sequence_for<types>);

	return invoke<types, offsets>(copy, buffer);
}

template<typename f, typename... template_args>
auto invoke(auto&&... runtime_args)
{
	return []<std::size_t... i>(std::index_sequence<i...>, auto&&... args) {
		return std::tuple{std::invoke(f<std::get<i>(template_args)...>,
		                              std::forward(args)...)...};
	}(std::forward(runtime_args));
}

	                             args...)...};
		return std::tuple{std::apply(func<std::get<i>(template_args)...>,
	                             args...)...};
ssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssss
	}(std::index_sequence<types>, std::move(runtime_args));
}

template<typename f, typename... template_args>
auto invoke(auto&&... runtime_args)
{
    template_args - оибо запаковываем в кортеж, либо обрабатываем рекурсивно

	return []<std::size_t... i>(std::index_sequence<i...>, auto&&... args) {
		return std::tuple{std::invoke(f<std::get<i>(template_args)...>,
		                              std::forward(args)...)...};
	}(std::forward(runtime_args));
}
template_args внутрь не протащить - возвращаемся к рекурсии



template<typename callable, typename... template_args>
auto invoke(auto&&... runtime_args)
{
	auto results = invoke<callable, tail_args>()
}

template<typename f, typename... template_args>
auto invoke(auto&&... runtime_args)
{
	return []<std::size_t... i>(std::index_sequence<i...>, auto&&... args) {
		return std::tuple{std::invoke(f<std::get<i>(template_args)...>,
		                              std::forward(args)...)...};
	}(std::forward(runtime_args));
}

каждый iterable - это std::tuple, индексируемый числом. Поэтому внутреннее разворацивание множит std::get, а внешнее множит это
уже размноженное хозяйство. Возможно даже через склеивание кортежей.

return {std::invoke(callable<std::get<i>(iterables)>..., std::forward(runtime_args)...)};

// template<class...> class integer_sequence

мне надо callable<std::get<i>(a), std::get<i>(b), ...>

для каждого параметра из пака:
- объединить их в кортеж
- применить ко всем элементам кортежа операцию std::get<i>

callable<std::get<i>(tupleoid1), std::get<i>(tupleoid2)>	


template<std::integral type, std::integral... others>
static std::tuple<type, others...>
parse_checked(std::span<const std::byte> buffer)
{
	const std::tuple value{*reinterpret_cast<const type *>(buffer.data())};
	const auto rest = buffer.subspan(sizeof(type));
	if constexpr (sizeof...(others) > 0) {
		return std::tuple_cat(value, parse_checked<others...>(rest));
	} else {
		return value;
	}
}

template<std::integral type, std::integral... others>
static std::tuple<type, others...>
parse_checked(std::span<const std::byte> buffer)
{
	const std::tuple value{*reinterpret_cast<const type *>(buffer.data())};
	const auto rest = buffer.subspan(sizeof(type));
	if constexpr (sizeof...(others) > 0) {
		return std::tuple_cat(value, parse_checked<others...>(rest));
	} else {
		return value;
	}
}

template<std::integral... types>
static std::optional<std::tuple<types...>>
parse(std::span<const std::byte> buffer)
{
	const size_t total_size = (0 + ... + sizeof(types));
	if (total_size <= buffer.size()) {
		return parse_checked<types...>(buffer);
	} else {
		return std::nullopt;
	}
}


apply_pairwise()
{
}

Попытаться развернуть рекурсию с помощью std::integer_sequence, точнее, index_sequence_for<types>
(избавимся от parse_checked):

Сначала генерируем массив "тип-смещение"
Затем для каждого элемента массива генерируем обработчик

смещение - это предыдущий элемент плюс смещение текущего. Либо просто 0 + ... + пак_до_текущего_элемента
при обеих подходах смещение выходит из шаблонного параметра

	auto extractor = []<std::size_t... i>(Tuple&& tuple, F&& f, std::index_sequence<i...>)



	std::generate(..., [current_offset]())

	const auto extractor = []<std::integral type, std::ptrdiff_t offset>
	                       (auto buffer) {
		return *reinterpret_cast<const type *>(buffer.data() + offset);
ssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssssss
	}

	return []<std::size_t... i>(auto buffer) {
		return (std::get<I>(extractor)(buffer), ...);
	}(buffer);



// Endianness-checking file signature
// DBBC = DutyBlasm ByteCode
static const std::uint16_t supported_signature = 0xdb'bc;

dutyblasm::bytecode_parser::~bytecode_parser()
{
}

template<std::integral T>
static std::tuple<std::optional<T>, std::span<const std::byte>::iterator>
pop(std::span<const std::byte>::iterator cursor, std::span<const std::byte>::iterator stream_end)
jjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjjj
{
	auto next_cursor = cursor + sizeof(T);
	bool no_overread = next_cursor < stream_end;
	return no_overread ? {std::bit_cast<T>(cursor), next_cursor} : {};
}

bool
dutyblasm::bytecode_parser::process(std::span<const std::byte> blob)
{
	std::span<const std::byte>::iterator cursor = stream.begin();

	std::optional<std::uint16_t> magic;
	std::optional<std::uint16_t> int_count;
	std::optional<std::uint16_t> real_count;
	std::optional<std::uint16_t> buffer_count;






	constexpr std::ptrdiff_t end_offset = field_offset + sizeof(field);

	using parser = std::tuple<get_field<field_type, field_offset>, >

	using <добавляем значения к возвращаемому кортежу> ... = ...<end_offset, next_fields>
}


buffer_part_parser<0, std::uint16_t, std::uint16_t>::parse(buffer); -- функциональный объект, прини



template<std::integral T>
static T
convert(std::span<const std::byte, sizeof(T)> in_buffer)
{
	return no_overread ? {std::bit_cast<T>(cursor), next_cursor} : {};
}

	std::ranges::generate()

	Можем приземлить во внутренний tuple, хранящий и смещение, и шаблонные функторы. Либо сразу функтор с шаблонным смещением.
	типа template<std::integral T, std::ptrdiff_t FieldOffset> -- лямбда?

	



	для каждого элемента (std::ranges::transform или std::views::transform):
		вычисляем смещение и размер, то есть подбуфер (std::span<T, sizeof(T)>)
		преобразуем в итератор
		выполняем преобразование bit_cast-ом. (или reinterpret_cast<T *> с разыменованием ///)
			или преобразуем содержимое подбуфера в тип

	return {, ...};


	Пример применения:
	auto header = read_fields<std::uint16_t, std::uint16_t, std::uint16_t, std::uint16_t>
	if (!header) {
		return false;
	}
	auto [signature, int_count, real_count, buffer_count] = *header;
	if (signature != supported_signature) {
		return false;
	}

	проверяем, что при чтении не выйдем за пределы буфера
}

	std::tie(version, cursor) = pop<std::uint8_t>(cursor, blob.end());
	std::tie(std::ignore, cursor) = pop<std::uint8_t>(cursor, blob.end());
	std::tie(int_count, cursor) = pop<std::uint16_t>(cursor, blob.end());
	std::tie(real_count, cursor) = pop<std::uint16_t>(cursor, blob.end());
	std::tie(buffer_count, cursor) = pop<std::uint16_t>(cursor, blob.end());
	if (!buffer_count) {
		return false;
	}

	if (*version )
	

	auto [version, next] = pop<std::uint8_t>(cursor, stream_end);
	if (!version || *version != supported_version) {
		return false;
	}
	cursor = next;

	auto [reserved, next] = pop<std::uint8_t>(cursor, stream_end);
	if (!reserved) {
		return false;
	}
	cursor = next;

	auto [global_ints_count, next] = pop<std::uint16_t>(cursor, stream_end);
	if (!global_ints_count) {
		return false;
	}
	cursor = next;

	auto [global_reals_count, next] = pop<std::uint16_t>(cursor, stream_end);
	if (!global_reals_count) {
		return false;
	}
	cursor = next;

	void on_constants(std::span<std::int64_t> values) override
	{
		print_list("integers", values, this->out);
	}

	void on_constants(std::span<double> values) override
	{
		print_list("reals", values, this->out);
	}
}

protected:
	virtual void on_constants(std::span<std::int64_t> values);
	virtual void on_constants(std::span<double> values);
	virtual void on_block_start();
	virtual void on_block_expression();
	virtual void on_block_end(std::uint8_t condition, std::uint8_t on_true,
	                          std::uint8_t on_false);
};

} /* namespace dutyblasm */


==================
#include <dutyblasm/bytecode.hpp>

class bytecode_printer : public dutyblasm::bytecode_parser {
	std::ostream_iterator<char> out;

public:
	bytecode_printer(std::ostream &utf8)
		: out(utf8)
	{
	}

protected:
	template <typename T>
	static void
	print_list(std::string_view title, std::span<T> items,
	           std::ostream_iterator<char> out)
	{
		std::format_to(out, "{}\n", title);
		for (std::int64_t item : items) {
			std::format_to(out, "  {}\n", item);
		}
	}

	void on_constants(std::span<std::int64_t> values) override
	{
		print_list("integers", values, this->out);
	}

	void on_constants(std::span<double> values) override
	{
		print_list("reals", values, this->out);
	}

	static const is_any_caller(std::span<std::uint16_t> caller_list)
	{
		return caller_list.empty();
	}

	struct captures {
		std::uint8_t caller_register_count;
		std::span<std::uint16_t> caller_ids;
		std::span<std::uint8_t> caller_register_ids;

		std::span<std::uint16_t> global_integer_ids;
		std::span<std::uint16_t> global_real_ids;
	}

	void on_new_block(std::uint16_t id, captures captures) override
	{
		std::format_to(this->printout, "block #{}\n", id);

00                     /* no known callers, expect any instead */
02                     /* 2 registers are captured from callers */
02                     /* 2 integers captured */
01                     /* 1 real captured */
00                     #0 = anycaller.#0
01                     #1 = anycaller.#1
00 00                  #2 = 0
00 01                  #3 = -1
00 00                  #4 = 2.0
	}


	void on_block_expression() override
	{
		в зависимости от 


		this->value_names[arg] ========== std::format('#{}') -- номер регистра или сразу значение
	                                  узнаём функцией get_value_label

		std::string &result_name = ;

		this->value_names[std::get<0>(args)];
		std::format_to(this->printout, "{} = {} {} {} {}", out_name, command, in_name);
		for (auto arg : args | std::ranges::views::take(arg_count[command])) {
			std::string &arg_name = this->value_names[arg];
			this->out << ' ' << arg_name;
		}
		this->out << '\n';
	}

	void on_block_end(std::uint8_t condition, std::uint8_t on_true,
	                  std::uint8_t on_false) override
	{
		std::format_to(this->out, "> #{} ? #{} : #{}\n", condition, on_true,
		               on_false);
	}
};

static std::optional<std::filesystem::path>
parse_cmdline(int argc, char *argv[])
{
	if (argc != 2) {
		return {};
	}
	return argv[1];
}

int
main(int argc, char *argv[])
{
	auto input_path = parse_cmdline(argc, argv);
	if (input_path) {
		std::string bytecode = read_file(input_path);
		bytecode_printer printer(std::cout);
		printer.process(bytecode);
		return EXIT_SUCCESS;
	} else {
		return EXIT_FAILURE;
	}
}
```

`src/util_dump.cpp`:

```cpp
#include <dutyblasm/bytecode.hpp>

class bytecode_printer : public dutyblasm::bytecode_parser {
	std::ostream_iterator<char> out;

public:
	bytecode_printer(std::ostream &utf8)
		: out(utf8)
	{
	}

protected:
	template <typename T>
	static void
	print_list(std::string_view title, std::span<T> items,
	           std::ostream_iterator<char> out)
	{
		std::format_to(out, "{}\n", title);
		for (std::int64_t item : items) {
			std::format_to(out, "  {}\n", item);
		}
	}

	void on_constants(std::span<std::int64_t> values) override
	{
		print_list("integers", values, this->out);
	}

	void on_constants(std::span<double> values) override
	{
		print_list("reals", values, this->out);
	}

	static const is_any_caller(std::span<std::uint16_t> caller_list)
	{
		return caller_list.empty();
	}

	struct captures {
		std::uint8_t caller_register_count;
		std::span<std::uint16_t> caller_ids;
		std::span<std::uint8_t> caller_register_ids;

		std::span<std::uint16_t> global_integer_ids;
		std::span<std::uint16_t> global_real_ids;
	}

	void on_new_block(std::uint16_t id, captures captures) override
	{
		std::format_to(this->printout, "block #{}\n", id);

00                     /* no known callers, expect any instead */
02                     /* 2 registers are captured from callers */
02                     /* 2 integers captured */
01                     /* 1 real captured */
00                     #0 = anycaller.#0
01                     #1 = anycaller.#1
00 00                  #2 = 0
00 01                  #3 = -1
00 00                  #4 = 2.0
	}


	void on_block_expression() override
	{
		в зависимости от 


		this->value_names[arg] ========== std::format('#{}') -- номер регистра или сразу значение
	                                  узнаём функцией get_value_label

		std::string &result_name = ;

		this->value_names[std::get<0>(args)];
		std::format_to(this->printout, "{} = {} {} {} {}", out_name, command, in_name);
		for (auto arg : args | std::ranges::views::take(arg_count[command])) {
			std::string &arg_name = this->value_names[arg];
			this->out << ' ' << arg_name;
		}
		this->out << '\n';
	}

	void on_block_end(std::uint8_t condition, std::uint8_t on_true,
	                  std::uint8_t on_false) override
	{
		std::format_to(this->out, "> #{} ? #{} : #{}\n", condition, on_true,
		               on_false);
	}
};

static std::optional<std::filesystem::path>
parse_cmdline(int argc, char *argv[])
{
	if (argc != 2) {
		return {};
	}
	return argv[1];
}

int
main(int argc, char *argv[])
{
	auto input_path = parse_cmdline(argc, argv);
	if (input_path) {
		std::string bytecode = read_file(input_path);
		bytecode_printer printer(std::cout);
		printer.process(bytecode);
		return EXIT_SUCCESS;
	} else {
		return EXIT_FAILURE;
	}
}
```

`include/dutyblas/bytecode.hpp`:

```cpp
#include <span>

namespace dutyblasm {

class bytecode_parser {
public:
	virtual ~bytecode_parser();

	void process(std::span<std::byte> blob);

protected:
	virtual void on_constants(std::span<std::int64_t> values);
	virtual void on_constants(std::span<double> values);
	virtual void on_block_start();
	virtual void on_block_expression();
	virtual void on_block_end(std::uint8_t condition, std::uint8_t on_true,
	                          std::uint8_t on_false);
};

} /* namespace dutyblasm */
```

`include/dutyblas/utility.hpp`:

```cpp
/** utility.hpp - useful general-purpose functions
 *
 * Copyright (c) 2022 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

#include <concepts>

namespace dutyblasm {

template<std::integral... types>
using sizeofs = std::index_sequence<sizeof(types)...>;

template<std::integral type, type head, type... tail>
constexpr type sum = head + sum<type, tail...>;

template<std::integral type, type head>
constexpr type sum<type, head> = head;

} // namespace dutyblasm
```

`vm.hpp`

## Standard Library `mjs` Coding Convention Example

```ecmascript
/* https://tc39.es/ecma262/#sec-performeval */
function PerformEval(x, callerRealm, strictCaller, direct) {
    /*  1     */ assert(direct || !strictCaller);
    /*  2     */ if (Type(x) != String) {
    /*        */     return x;
    /*        */ }
    /*  3     */ let evalRealm = getRunningExecutionContext().Realm;
    /*  4     */ q(HostEnsureCanCompileStrings(callerRealm, evalRealm));
    /*  5     */ let inFunction = false;
    /*  6     */ let inMethod = false;
    /*  7     */ let inDerivedConstructor = false;
    /*  8     */ let inClassFieldInitializer = false;
    /*  9     */ if (direct) {
    /*   .a   */     let thisEnvRec = bang(GetThisEnvironment());
    /*   .b   */     if (thisEnvRecord.type == FunctionEnvironmentRecord) {
    /*   .i   */         let F = thisEnvRecord[FunctionObject];
    /*   .ii  */         inFunction = true;
    /*   .iii */         inMethod = thisEnvRec.HasSuperBinding();
}
```

## Unicode table range array generator

`tools/ucd2hpp_template.hpp.in`:

```cpp
/** {{out_name}} - a property tester for Unicode code points
 *
 * Generated from Unicode Character Database {{ucd_version}} by tools/ucd2hpp.py.
 *
 * ucd2hpp.py license header:
 *
 * > Copyright 2021 Oleg Iarygin <oleg@arhadthedev.net>
 * > 
 * >  Licensed under the Apache License, Version 2.0 (the "License");
 * >  you may not use this file except in compliance with the License.
 * >  You may obtain a copy of the License at
 * >
 * >      http://www.apache.org/licenses/LICENSE-2.0
 * >
 * >  Unless required by applicable law or agreed to in writing, software
 * >  distributed under the License is distributed on an "AS IS" BASIS,
 * >  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * >  See the License for the specific language governing permissions and
 * >  limitations under the License.
 *
 * Unicode Character Database license header:
 *
 * > (c) {{ucd_copyright_year}} Unicode (R), Inc.
 * >
 * > Unicode and the Unicode Logo are registered trademarks of Unicode, Inc.
 * > in the U.S. and other countries. For terms of use, see
 * > http://www.unicode.org/terms_of_use.html
 * >
 * > Unicode Character Database
 * > For documentation, see http://www.unicode.org/reports/tr44/
 */

#ifndef {{out_id}}_H
#define {{out_id}}_H

struct range {
	uint16_t start;
	uint16_t length;
};

static const range unicode_codepoint_ranges[] = {
{{unicode_codepoint_ranges}}
};

static const unicode_plane planes[15] = {
{{unicode_codepoint_planes}}
	/* U+Fxxxx and U+10xxxx are for noncharacter Private Use Area */
};

#endif /* {{out_id}}_H */
```

`tools/ucd2hpp.py` (heavily unfinished):

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
# © 2019 and later: Unicode, Inc. and others.
# License & terms of use: http://www.unicode.org/copyright.html

# This script reads Unihan*.txt files and writes one new file per property.
# Invoke with one argument, the path to the Unihan data folder.

from itertools import group
from pathlib import Path
from typing import TextIO 

http://www.unicode.org/L2/L1999/UnicodeData.html
http://www.unicode.org/Public/5.1.0/ucd/UCD.html


Code value
Genera

Precision in Version Citation
>  The Unicode Standard, Version 3.1.1
2. The Unicode Standard, Version 3.1    [equivalent to 3.1.0]
3. The Unicode Standard, Version 3.0 or later
4. The Unicode Standard

# <http://www.unicode.org/Public/5.1.0/ucd/UCD.html#UCD_File_Format>:
#
# > Each line of data consists of fields separated by semicolons. The fields are
# > numbered starting with zero. Code points are expressed as hexadecimal
# > numbers with four to six digits. They are written without "U+". Within
# > a sequence of code points, spaces are used for separation. Leading and
# > trailing spaces within a field are not significant.

# <http://www.unicode.org/Public/5.1.0/ucd/UCD.html#UCD_File_Format>:
#
# > For binary properties, the second field given is the name of the applicable
# > property, with the implied value of the property being "True". Only the
# > ranges of characters with the binary property value of True are listed.
# > For example (from PropList.txt):
# >
# >      1680       ; White_Space # Zs      OGHAM SPACE MARK
# >      180E       ; White_Space # Zs      MONGOLIAN VOWEL SEPARATOR
# >      2000..200A ; White_Space # Zs [11] EN QUAD..HAIR SPACE

# <http://www.unicode.org/Public/5.1.0/ucd/UCD.html#UCD_File_Format>:
#
# > For backwards compatibility, in the file UnicodeData.txt a range is
# > specified not by the form "X..Y", but by an entry for the start and end
# > characters of the range. Instead of a character name in the name field,
# > a range identifier, followed by a comma and the string "First", in angle
# > brackets, is given for the start character: <CJK Ideograph, First>.

# <http://www.unicode.org/Public/5.1.0/ucd/UCD.html#UCD_File_Format>:
#
# > Hash marks ("#") are used to indicate comments: all characters from the hash
# > mark to the end of the line are comments, and disregarded when parsing data.
# > In many files, the comments on data lines use a common format.

from collections import defaultdict
from itertools import chain, groupby, pairwise
from typing import Iterator

def rows_to_range(row_pair: tuple[tuple[str, str, str], tuple[str, str, str]]) -> Iterator[tuple[int, int, str]]:
    (code_f name_f, property_f), (code_f, name_f, property_f) = row_pair
    if name_f.endswith(", First>"):
        assert name_l.endswith(", Last>")
        assert property_f == property_l
        return (int(code_f), int(code_l), property_f)
    else:
        code = int(code_f)
        return (code, code, property_f)


def load_ucd(path: Path) -> dict[str, list[tuple[int, int]]:
    with open(path / "UnicodeData.txt", "r", encoding="utf8") as db:
        core = (";".split(row)[0, 2] for row in db)

    with open(path / "DerivedCoreProperties.txt", "r", encoding="utf8") as db:
        lines = (expr for expr in ("#".split(line)[0] for line in file) if expr)
        nonjoined = ((column.strip() for column in ";".split(line)] for row in db)
        derived = return (rows_to_range(row_pair) for row_pair in pairwise(rows))

    return chain(core, derived)


def get_code_ranges(ucd: Iterator[tuple[int, int, srt]], properties_of_interest: set[str]) -> Iterator[tuple[int, int, set[str]]]:
    filtered = [row for row in ucd if row[2] in properties_of_interest]
    sorted = sort(filtered, lambda row: "{}-{}".format(row[2], row[0]))
    grouped = list(groupby(sorted, lambda row: row[2]))

    baseline_codepoint = 0x0000
    while grouped:
        # ищем минимальное значение верхней границы среди тех, чья нижняя граница не выше базовой линии
        # это значение - окончание текущего диапазона
        # создаём выходной диапазон, начало - текущая базовая линия, конец - найденна граница, свойства - свойство в каждом диапазоне, нижняя граница которого ниже текущей (у таких верхн будет не ниже найденной)
        # устанавливаем у всех элементов нижнюю границу в курсор
        # если какой диапазон оказался пустым - удаляем его

					 
required_properties = [record for derived in all_records if ... in ]


        # records = 
    ranges = [(int("..".split(value)), grouped) for value, grouped in records]

    using открываем UnicodeData.txt:
         = defaultdict([])
        считываем оттуда свойства, добавляя в defaultdict


def get_derived_properties(file: TextIO) -> list[tuple[int, int, str]]:
    """Parse content of DerivedCoreProperties.txt into a list of tuples"""
    lines = (expr for expr in ("#".split(line)[0] for line in file) if expr)
    records = ([column.strip() for column in ";".split(line)] for line in lines)
    ranges = [(int("..".split(value)), grouped) for value, grouped in records]
    return {grouped[0][1]: [items[0] for items in grouped] \
            for grouped in group(ranges, lambda range: range[1])}

def flatten_range_groups(range_groups: list[tuple[int, int, str]], properties_of_interest: set[str]):

    requested_properties = {name: ranges for name, range in range_groups.items() if name in properties_of_interest}

    input_common_start = 0
    while any(range for range in range_groups if range_groups.values() in requested_properties):
        # [отфильтровываем список, оставляя элементы, у которых начало не раньше глобальной границы]
        # среди всех элементов списка находим элемент ~~с началом не раньше глобальной границы и~~ наименьшей верхней границей
        # создаём в выходном массиве элемент с диапазоном от базы до найденного значения (включительно?), запихиваем к нему в классы имена оставшихся массивов
        # переписываем у каждой головы массивов начало первого элемента на найденное значение, если длина диапазона стала равна нулю - удаляем


    # берём очередной символ из всех диапазонов. Если он один и тот же - добавлем в текущий диапазон; покрываемые диапазоном свойства определяем из первого элемента. Сразу пишем метод обобщённым (range, props). Если начинаются отличия - заканчиваем набор, смотрим новый набор свойств и открываем новый набор с ним

    # Сначала строим итератор над массивом диапазона, чтобы он возвращал пару "кодовая точка - принадлежит ли".
    # Затем сшиваем по ключу в помощью ()

    # Ситуация: один диапазон заканчивается раньше других. Оканчиваем выходной диапазон и сдвигаем нижнюю границу для сравнения; держим в доступе головы от списков, трактуя их как очереди.

    # для всех диапазонов, в которых начало диапазона не больше текущей границы, находим минимальную верхнюю границу
    # эта граница - наименьший общий знаменатель, 


# диапазоны разных свойств могут пересекаться частично
# поэтому сначала опписываем символы для каждой отдельной кодо... получаем слишком много ()

# Так что сначала убираем неинтересные диапазоны, затем 

# находим пересечения и распиливаем по их границам - обычный цикл

# Либо сливаем диапазоны воедино, распиливая каждый из диапазонов
# После этого проверяяем из основной функции, что все элементы имеют принадлежность к ID_Continue
# Разделяем на страницы, не обращая внимание на атрибуты
# Генерируем заголовок

# Считываем свёрнутые
# Отбрасываем неинтересные
    foodict = {k: v for k, v in mydict.items() if k in properties_of_interest}
# Оставшиеся разворачиваем до отдельных кодовых точек
# Затем сначала собираем пары "ненормированный диапазон-свойство"
# Затем закрываем файлы, склеиваем списки и, проходя по всем запясям, 


def get_derived_properties(file: TextIO) -> dict[str, list[tuple[int, int]]]:
    """Parse content of DerivedCoreProperties.txt and group it by property"""
    lines = (expr for expr in ("#".split(line)[0] for line in file) if expr)
    records = ([column.strip() for column in ";".split(line)] for line in lines)
    ranges = [(int("..".split(value)), grouped) for value, grouped in records]
    return {grouped[0][1]: [items[0] for items in grouped] \
            for grouped in group(ranges, lambda range: range[1])}


def get_shared_map(grouped: dict[str, list[tuple[int, int]]], properties: set[str]):
    выбираем интересующие свойтва
    проставляем множество принадлежности к элементу


def get_general_categories(file: TextIO) -> dict[str, list[tuple[int, int]]]:
    """Parse content of UnicodeData.txt and group it by general category"""
    code_points = ((";".split(line))[0:3] for line in file)


def parse_unicodedata(file: TextIO) -> dict[str, list[tuple[int, int]]]:
    """
    See <http://www.unicode.org/L2/L1999/UnicodeData.html> for documentation
    on UnicodeData.txt file structure.
    """
    records = ((";".split(row))[0:3] for row in database)
    margables = ((val, name.endswith("rst>"), cat) for val, name, cat in records)
    # ranges = 

    # Fist>, Last> склеиваем попарно с помощью itertools.group()
    # Следующим шагом преобразуем одно- и двухэлементные диапазоны в кортежи атрибутов
    # Затем снова склеиваем, но уже по расенству атрибутов
    # Нормализуем диапазоны, преобразуя списки кортежей с списки кортежей
    # Восстанавливаем плоскоту списка


    # обработать запись и опционально поглотить следующую
    # itertools.compress(data, selectors) -- для удаления записи (if в comprehensions тоже хорош, а потому используем его)


# 1 Считываем в кортежи "номер, тип (начало-конец-единичный), general category"
# 2 Объединяем записи First и Last в одну (если last не следуем за first - это ошибка), удаляем тип записи
# 3 

# 20000;<CJK Ideograph Extension B, First>;Lo;0;L;;;;;N;;;;;
# 2A6DF;<CJK Ideograph Extension B, Last>;Lo;0;L;;;;;N;;;;;


def get_categories(ucd: Path) -> dict[str, list[tuple[int, int]]]:
    categories = {}
    with open(ucd / "UnicodeData.txt", "r", encoding="utf8") as db:
        categories.update(parse_unicodedata(db))
    with open(ucd / "DerivedCoreProperties.txt", "r", encoding="utf8") as db:
        categories.update(parse_derivedcoreproperties(db))
    return categories


# This scripts allows to process the most fresh version of the Unicode Character Database (UCD) without waiting for
# the next Python version to use its Unicode utilities.

# To not depend on Unicode routines

from unicodecategories import categories


"""
This script is created to process the UCD as soon as .
"""

from argparse import ArgumentParser
from itertools import groupby


# 1 Считываем из файла по properties: list[str], получаем map[str, list[tuple[int, int]]]
# 2 Выворачиваем отображение, перемещая properties из ключа корневой структуры в свойства диапазона. Заодно сливаем элементы map

"""
Note: none of the returned ranges crosses a border between Unicode planes thanks
to two noncharacters in the end of each plane (U+...FFEF and U+...FFFF).
"""


def read_property_ranges(input_path: Path): tuple[int, int]
    

    # открыть файл
    # прочитать построчно:
        # распознать первый и последний элемент диапазона
        # если они принадлежат разным страницам - распилить диапазон

        # промежуточный результат: диапазоны для первых символов и диапазоны дл продолжений
        # там, где они пересекаются - распиливаем и выставляем общие флаги. Возможно, стоит использовать фУНКЦИЮ СЛИЯНИЯ ДИАПАЗОНОВ


OrderedRange = tuple[int, int]
UnicodePlane = list[OrderedRange]


_RANGE_FORMAT = "\t{{{:#04x}, {:#04x}}},\n"


def get_plane_id(code_point: int) -> bool
    return code_point // 2 ** 16;


def group_ranges_by_planes(ranges: list[OrderedRange]) -> list[UnicodePlane]:
    assert all([get_plane_id(first) == get_plane_id(last) for first, last in ranges])
    return groupby(ranges, lambda range: get_plane_id(range[0]))


def format_planes(planes: list[UnicodePlane]) -> str:
    formatted = ["/* U+{}xxxx */ {{{}}}".format(len(plane)) for plane in planes]
    return ",\n\t".join(formatted)


def format_range__(plane: UnicodePlane) -> str:
    formatted = ["\t{{{:#04x}, {:#04x}}},\n".format(first, last) for first, last in plane]
    return ",\n\t".join(formatted)


def format_ranges(planes: list[UnicodePlane]) -> str:
    format_range = lambda first, last: "\t{{{:#04x}, {:#04x}}},\n".format(first, last)
    formatted = range_body = 
    return ["\t{{{:#04x}, {:#04x}}},\n".format(format_range(range)) for range in ranges]

В начале каждого диапазона - "\t/* U+0xxxx */\n"

    "/* U+0xxxx */
    {0x0041, 0x005a - 0x0041},
    {}"

    formatted_ranges = [] .join(", ")

    formatted_planes = []

    formatted_planes = [_PLANE_FORMAT.format(id, len(children)) for id, children in enumerate(unicode_planes)]

def fill_header(input_path: Path, output_path: Path,
                unicode_planes: list[UnicodePlane]) -> None:
    with open(input_path, "r", encoding="utf8") as input_file:
        input_text = input_path.read()

    formatted_planes = format_planes()
    formatted_ranges = format_ranges()
    output_header = template.format(out_name = output_path.name,
                                    out_id = output_path.stem.upper(),
                                    unicode_codepoint_planes = formatted_planes,
                                    unicode_codepoint_ranges = formatted_ranges)

    with open(output_path, "w", encoding="utf8") as output_file:
        output_file.write(output_header)


if __name__ == "__main__":
    arguments = parse_command_line_arguments()
    if arguments:
        convert_ucp_to_hpp(arguments)
```