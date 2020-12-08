# Structured Commenting
 A specification of my structured commenting scheme

Most existing commenting techniques work adiquatly when notating a few code lines but become unclear or in need of interpretation when used on large structured code segments. To signify that a comment is talking about multiple lines of code, most programmers typically block multiple lines of code under that comment without spacing between them, or leave their comment right before structural code block such as a function or loop in order to describe that structure as a whole. But what if you need to describe both a structure and it's sub components, and what if those sub components have sub components of their own? This is where this structured commenting scheme can help.

## Two types of comments
To indicate that a comment is only describing the line directly below it, 

```c++
#This comment only talks about the line directly below it
int foo = 0

int bar = 1
```

To indicate that a comment is describing multiple lines.
```c++
string alice = "Hello "
string bob = "World!"

//this comment describes the next 3 lines
    int a = 2
    int b = 3
    int c = a + b

int foo = bar
```

## Usage

An example of both comment types in use together
```c++
//graphics controller
    for(uint8_t i = 0; i < 9; i++){

        //where do you want to write data
        graphicsControllerIndexPort.Write(i);

        //what do you want to write there
        graphicsControllerDataPort.Write(*(registers++));
    }

//attributeController
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
```

Nesting comments talking about multiplel lines. Each instance of ``...`` would be replaced by the appropriate code segment.
```c++
//make pizza
    //walk to kitchen
        //face twords kitchen
            ...
        //steppy step step
            ...
    //shape dough into circle
        ...
    //add sauce
        //grab ladle
            ...
        //dunk ladle in sauce
            ...
        //place ladle over pizza
            ...
        //tilt ladle back 90 degrees
            ...
    //add cheese
        //pick up cheese
            ...
        //sprinkle cheese over pizza
            ...
    //bake
        //pick up pizza
            ...
        //walk to oven
            ...
        //place in oven
            ...
    //cut
        //grab pizza cutter
            ...
        //loop x4
            ...
            //cut pizza vertically
                ...
            //rotate pizza 12.5 degrees
                ...
```

## Benefits
- Increased readability
- No ambiguity about what lines of code a comment is refering to
- Comment based [code folding](https://en.wikipedia.org/wiki/Code_folding)
- Is or can be made compatable with code formatters

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

- Code formatters (such as [prettier](https://prettier.io/)) may change the whitespace depth of code lines inside in multi-line comments. Putting an ``if True:`` statement after each multi-line comment will fix this.

- It is acceptable to use the multi-line comment scheme to talk about single lines of code. This can sometimes make dense code more readable, and avoid the need to insert empty whitespace between code lines. Choosing whether to do this is a matter of taste.

Visually clear:
```c++
#Declare first variable
    int foo = 0
#Declare second variable
    int bar = 1
#Add variables together
    int buzz = foo + bar
```

Visually convoluted:
```c++
#Declare first variable
int foo = 0
#Declare second variable
int bar = 1
#Add variables together
int buzz = foo + bar
```
