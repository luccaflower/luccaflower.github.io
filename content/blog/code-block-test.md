---
title: "Code blocks"
description: "Keeping the snippets consistent with the theme"
date: git Last Modified
---
In my quest to do as little web-development as possible when making this
site, I decided to just fork this [base blog](link-here) repo, and make
some minor changes to the CSS to fit my needs (such as setting this
lovely gruvbox-dark color-scheme). As it turns out, this base blog comes
with the [syntax highlighting plugin](link-here) configured, which uses
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
