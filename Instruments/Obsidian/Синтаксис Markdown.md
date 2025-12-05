---
tags:
  - Markdown
  - "#Markdown_syntax"
links: https://help.obsidian.md/Home
---
# Markdown Cheat Sheet

Thanks for visiting:
[The Markdown Guide](https://www.markdownguide.org)!
[Obsidian Help](https://help.obsidian.md/Home)

This Markdown cheat sheet provides a quick overview of all the Markdown syntax elements. It can’t cover every edge case, so if you need more information about any of these elements, refer to the reference guides for [basic syntax](https://www.markdownguide.org/basic-syntax/) and [extended syntax](https://www.markdownguide.org/extended-syntax/).

---
## <font color="purple">1. Basic Syntax</font> 

These are the elements outlined in John Gruber’s original design document. All Markdown applications support these elements.

---
### Heading
# H1
## H2
### H3

#### H4

##### H5

###### H6

---
### Bold

**bold text**

---
### Italic

*italicized text*

---
### Colored text
<font color="purple"> - This text is purple</font>
<font color="green"> - This text is green</font>
<font color="red"> - This text is red</font>

---
### Blockquote

> blockquote
---
### Ordered List

1. First item
2. Second item
3. Third item

---
### Unordered List

- First item
- Second item
- Third item

---
### Code

`code`

---
### Java code block
```java
import java.util.Scanner;  
  
public class Factorial {  
    public static void main(String[] args) {  
        Scanner sc = new Scanner(System.in);  
        int b = sc.nextInt();  
        int fact = 1;  
        for (int i = 1; i <= b; i++) {  
            fact *= i;  
        }  
        System.out.println("Факториал числа " + b + " равен " + fact);  
    }  
}
```

---

### Horizontal Rule

---

### Link

[Markdown Guide](https://www.markdownguide.org)

---
### Image

![name_of_pic](https://www.markdownguide.org/assets/images/tux.png)



## <font color="purple">2. Extended Syntax</font>

These elements extend the basic syntax by adding additional features. Not all Markdown applications support these elements.

---
### Table

| Syntax | Description |
| ----------- | ----------- |
| Header | Title |
| Paragraph | Text |

---
### Fenced Code Block

```
{
  "firstName": "John",
  "lastName": "Smith",
  "age": 25
}
```

---
### Footnote

Here's a sentence with a footnote. [^1]

[^1]: This is the footnote.

---
### Heading ID

### My Great Heading {#custom-id}

---
### Definition List

term
: definition

---
### Strikethrough

~~The world is flat.~~

---
### Task List

- [ ] Write the press release
- [ ] Update the website
- [ ] Contact the media

---
### Emoji

That is so funny! :joy:

(See also [Copying and Pasting Emoji](https://www.markdownguide.org/extended-syntax/#copying-and-pasting-emoji))

---
### Highlight

I need to highlight these ==very important words==.

---
### Subscript

H~2~O

---
### Superscript

X^2^

---
### Callout
> [! Warning]
> **Test Callout**

---
### Collapsed Callout
> [!info]-
> test
> test

---
### Faq Callout
>[!faq]
>123

---
### Note Callout
> [!note]
> *123*


## Basic
- [ ] to-do
- [/] incomplete
- [x] done
- [-] canceled
- [>] forwarded
- [<] scheduling

## Extras
- [?] question
- [!] important
- [*] star
- [i] quote
- [l] location
- [b] bookmark
- [i] information
- [x] savings
- [I] idea
- [p] pros
- [c] cons
- [f] fire
- [k] key
- [w] win
- [u] up
- [d] down
- [D] draft pull request
- [x] open pull request
- [x] merged pull request