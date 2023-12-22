Shipped with [[Desktop Application]]s and [[Web Service Backend]]s. Contains HOWTOs and advices on advanced (hacker grade) use of separate parts of the application together with each other and external tools.

## Self-documenting code

```cpp
/* grammar2code.hpp - public interface of libgrammar2code
 *
 * For code documentation see ../docs/ or `man grammar2code::<entity_name>`.
 *
 * Copyright (c) 2021 Oleg Iarygin <oleg@arhadthedev.net>
 *
 * Distributed under the MIT software license; see the accompanying
 * file LICENSE.txt or <https://www.opensource.org/licenses/mit-license.php>.
 */

#ifndef GRAMMAR2CODE_H
#define GRAMMAR2CODE_H

namespace grammar2code {

enum class inheritance {
	always,
	on_presence,
	on_absence
}

struct terminal_symbol {
	std::string utf8_content;
	std::unordered_map<std::string, inheritance> parameters;
};

struct nonterminal_symbol {
	std::string utf8_name;
};

struct restriction {
	nonterminal_symbol disallowed_symbol;
};

struct empty_value {
};

struct lookahead {
	bool is_positive;
	std::set<std::variant<terminal_symbol, nonterminal_symbol>> variants;
};

struct symbol {
	bool optional;
	lookahead lookahead_condition;
	std::vector<std::variant<terminal_symbol, nonterminal_symbol, restriction>> value;
};

struct descriptive {
	std::string text;
};

struct right_hand_side {
	std::pair<std::string, inheritance> inclusion_condition;
	std::variant<empty_value,
	             restriction,
	             descriptive,
	             std::vector<symbol>> symbol_sequence;
	lookahead lookahead_condition;
};

/* Production is a definition of a nonterminal */
struct production {
	std::unordered_set<std::string> parameters;
	std::unordered_set<right_hand_side> alternatives;
};

grammar
load_grammarkdown(std::string_view text);

parser_nfa
get_nfa_stream_parser(const grammar &input);

};  /* namespace grammar2code */

#endif  /* GRAMMAR2CODE_H */
```