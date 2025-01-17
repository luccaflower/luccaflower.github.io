---
title: "Code blocks"
description: "Keeping the snippets consistent with the theme"
date: 2024-12-28T19:00:00Z
---
In my quest to do as little web-development as possible when making this
site, I decided to just fork this [base blog](https://github.com/11ty/eleventy-base-blog/) repo, and make
some minor changes to the CSS to fit my needs (such as setting this
lovely gruvbox-dark color-scheme). As it turns out, this base blog comes
with the [syntax highlighting plugin](https://www.11ty.dev/docs/plugins/syntaxhighlight/) configured, which uses
PrismJS to do syntax-highlighting on code snippets. It even comes with a
few preset CSS themes. Now, none of those themes were gruvbox-dark, but
no problem, I just copied the solarized theme and changed the colors to
(mostly) match my neovim color-scheme. I think it turned out quite
nicely:
```c
//comment
int main(int argc, char** argv) {
	printf("Hello, World!");
}
```
```rust
fn main() {
	println!("Hello, World");
}
```
```java
public class Main {
	public static void main(String[] args) {
		System.out.println("Hello, World!");
	}
}
```
