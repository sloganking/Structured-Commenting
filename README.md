# Structured Commenting
This ReadMe outlines the specification for structured commenting.

**Note:** A basic formatter that can enable code formatter support for structured commenting in your language, can be [found here](https://github.com/sloganking/scfmt). 

Most existing commenting techniques work adiquatly when notating a few code lines but become unclear or in need of interpretation when used on large structured code segments. To signify that a comment is talking about multiple lines of code, most programmers typically block multiple lines of code under that comment without spacing between them, or leave their comment right before structural code block such as a function or loop in order to describe that structure as a whole. But what if you need to describe both a structure and it's sub components, and what if those sub components have sub components of their own? This is where structured commenting can help.

## Two styles of structured comments

Structured comments are special comments that use indendation to explicitly state which lines they are describing. There are two styles of structured comments that you can use for your project. ``bracketless`` and ``bracketed``.

### Bracketless

bracketless strucutred comments, use indentation alone to describe which lines they are refering to. This means creating a bracketless structured comment is as easy as writing a comment, and then indenting the lines under it that you want the comment to describe. The downside to this approach is that if a colleage or a code formatter changes indentation in a way that unintentionally modifies a bracketless structured comment, it becomes very difficult to figure out what indentation was intended before. 

```c++
string alice = "Hello "
string bob = "World!"

// this is a bracketless structured comment that describes the next 4 lines
    int a = 2
    int b = 3
    // add the two together (notice how this comment is part of what's being described by the structured comment above)
    int c = a + b

int foo = bar
```

### Bracketed

Bracketed strucuted comments use ``>`` and ``<`` characters at the beginning of single line comments, to indicate that they are a bracketed strcutured comment, and explicitly state what kind of bracketed strucuted comment they are, (opening, closing, both). These are akin to oppening and closing brackets ``{``, ``}`` used to indicate the start and end of some structure in many programming languages. Only they indicate the start and end of what a structured comment is describing, instead. You can think of ``>`` as an arrow saying "the lines below me are one indendation level deeper (to the right) than the lines above me. and ``<`` stating the opposite.

```c++
string alice = "Hello "
string bob = "World!"

//> this is a bracketed structured comment that describes the next 4 lines
    int a = 2
    int b = 3
    // add the two together (notice how this comment is part of what's being described by the structured comment above)
    int c = a + b
//<

int foo = bar
```

``<>`` can be used as short hand for closing an existing and opening a new structured comment, on the same line. For example

```c++
string alice = "Hello "
string bob = "World!"

//> this is a bracketed structured comment that describes the next 4 lines
    int a = 2
    int b = 3
    // add the two together (notice how this comment is part of what's being described by the structured comment above)
    int c = a + b

//<> this is a bracketed structured comment that describes the next 3 lines
    int d = 2
    int e = 3
    int f = d + e
//<

int foo = bar
```

### Which style should I use? 

Both styles work. Bracketless structured comments are a little easier to write. However Bracketed strucuted comments are more forgiving in that tools such as [scfmt](https://github.com/sloganking/scfmt) can recover their correct indentation if it is acidentally messed up. It is for this reason that I personally reccomend using bracketed structured comments instead of bracketless. However you're free to choose what works for you.

## Examples

Structured comments with code and traditional comments inside them.
```c++
//> graphics controller
    for(uint8_t i = 0; i < 9; i++){

        //where do you want to write data
        graphicsControllerIndexPort.Write(i);

        //what do you want to write there
        graphicsControllerDataPort.Write(*(registers++));
    }

//<> attributeController
    for(uint8_t i = 0; i < 21; i++){

        //must reset attributeController before sending data
        attributeControllerResetPort.Read();

        //where do you want to write data
        attributeControllerIndexPort.Write(i);

        //what do you want to write there
        attributeControllerWritePort.Write(*(registers++));
    }
    attributeControllerResetPort.Read();
    attributeControllerIndexPort.Write(0x20);
//<
```
Nesting structured comments inside one another

```rust
// remove comment notation if it exists
let (is_a_comment, space_after_comment_starter, line_no_comment_starter) =
    remove_comment_notation_if_it_exists(line_no_leading_spaces, comment_starter);

//> apply whitespace depth
    if is_a_comment & line_no_comment_starter.starts_with('>') {
        formatted_lines.push(line.to_string() + "\n");

        //> add comment to comment tracker
            let comment = CommentDetail {
                line: i,
                depth: leading_spaces.unwrap(),
            };
            comment_tracker.push(comment);
        //<
    } else if is_a_comment & line_no_comment_starter.starts_with("<>") {
        ...
    }
//<
```

Commenting out sections of your code for debuging, while still preserving obvious notation of what that code does.

```rust
let converted = add_brackets(&contents, extenstion)?;

//> write file
    // // leave file alone if there was no change
    // if converted != contents {
    //     let mut output = match File::create(file) {
    //         Ok(x) => x,
    //         Err(_) => return Err(ScfmtErr::CantCreatFile),
    //     };

    //     match write!(output, "{}", converted) {
    //         Ok(x) => x,
    //         Err(_) => return Err(ScfmtErr::CantWriteToFile),
    //     };
    // }
//<

Ok(())
```

## Benefits
- Increased readability
- No ambiguity about what lines of code a comment is refering to
- Because structured comments are indented, some IDEs will allow structured comment based [code folding](https://en.wikipedia.org/wiki/Code_folding)
- Is or can be made compatable with code formatters
- You can keep using your code formatter of choice, without waiting for them to add structured comment support, thanks to [scfmt](https://github.com/sloganking/scfmt)

## Notice
- Whitespace enforced languages (such as python) may require the programmer to insert additional lines of code (``if True:`` statements) to support this repo's multi-line commenting scheme.

```python
# initialize
if True:
    from compress import textCompressor
    compressor = textCompressor()

# tests
if True:
    testCompression()
    testDecompression()
    print("Everything passed")
```

- It is acceptable to use structured comments to talk about single lines of code. This can make dense code more readable, and avoid the need to insert empty whitespace between code lines. Choosing whether to do this is a matter of taste.

clear but uncompact:
```c++
#Declare first variable
int foo = 0

#Declare second variable
int bar = 1

#Add variables together
int buzz = foo + bar
```

Compact but hard to read:
```c++
#Declare first variable
int foo = 0
#Declare second variable
int bar = 1
#Add variables together
int buzz = foo + bar
```

Compact and clear:
```c++
#> Declare first variable
    int foo = 0
#<> Declare second variable
    int bar = 1
#<> Add variables together
    int buzz = foo + bar
#<
```

# FAQ

### Where did the name ``structured comment`` come from?

The name of **structured comments** is a homage to [Structured_programming](https://en.wikipedia.org/wiki/Structured_programming)

> a programming paradigm aimed at improving the clarity, quality, and development time of a computer program by making extensive use of the structured control flow constructs of selection (if/then/else) and repetition (while and for), block structures, and subroutines. 

Among other things, structured programming popularized code block structures and code block indentation. However comments seemingly never recieved the same upgrade. This is what structured commenging aims to fix.

I thought about going with the name ``block comments`` or ``nested comments`` (which could have made for some cool cover art of a bird tending to it's children in a nest. With  ``//>`` being birds, and ``>`` being their beak), but those names seem to be already being used in some places.

### What about the argument that comments should be uses as little as possible?

Some programmers attest that comments cannot be trusted because they may be incorrect, or the code that they were describing may have changed. Thus the best solution is to just make your code as understandable as possible without comments, and to use comments only where understanding without comments is not possible. While this is not a view I share, I'm not here to tell you when to write comments. If you don't need to write a comment, then don't. If you do, well structured comments are another tool in your toolbox with the potential to make your comments even better :).

### Does this change how normal comments work?

No. traditonal single line comments will mean what they always have. Whether that's a slightly ambiguous explanation of then next line(s?), commenting out code you're not using currently, or whatever else they were originally intended for. This means that you can slowly add structured comments to your existing code base without changing the meaning or interpretation of existing comments.

### When to use structured comments vs functions?

Choosing between a structured comment and a funciton, for non repeating code, is a matter of taste. My personal recomendation is to use structured comments for short (50 or less) lines of code, and functions for anything longer.

Wrapping some code in a structured comment is easy and fast, but you can forget you're inside a structured comment if they are too large.

The problem with functions is that attempting to turn a large section of code into a function may mean you need 8+ paramaters which you have to write twice, once as paramaters and once as arguments. Which takes a while and is a bit ugly in the end. 

It's up to you, but I'd personally use structured comments for short sections. As you don't have to deal with a scope change, paramater defining, argument passing, and return values, that comes with defining a function.

### Is there really demand for this, or is this a solution looking for a problem?

Seemingly yes, there is already existing demand for comments that describe explicit sections of code.

- Hyperledger Sawtooth: https://github.com/hyperledger/sawtooth-sdk-rust/blob/46e610008790a03b194b5baa24adb80243757366/src/consensus/service.rs#L23-L65
- Linux Kernel: https://github.com/torvalds/linux/blob/20855e4cb361adeabce3665f5174b09b4a6ebfe6/fs/xfs/libxfs/xfs_attr.h#L45
- Crafing Interpreters (slightly different usecase but still interesting):
    - https://github.com/munificent/craftinginterpreters/blob/01e6f5b8f3e5dfa65674c2f9cf4700d73ab41cf8/c/chunk.c#L5
    - https://github.com/munificent/craftinginterpreters/issues/1049
- intellij/android regions https://stackoverflow.com/a/26872292/9671390
- line seperators https://stackoverflow.com/a/2379350/9671390
- Astro8-Computer https://github.com/sam-astro/Astro8-Computer/blob/0c5b1bc1f998ccc856bc99476546e427847b3bae/example_armstrong_programs/pong.arm#L4
