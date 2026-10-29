# 4D Notepad

**A console-based text editor built with custom linked lists and a queue in C++.**

[![Language](https://img.shields.io/badge/Language-C%2B%2B-00599C?logo=cplusplus&logoColor=white)](https://isocpp.org/)
![Platform](https://img.shields.io/badge/Platform-Windows-0078D6?logo=windows&logoColor=white)
![IDE](https://img.shields.io/badge/IDE-Visual%20Studio-5C2D91?logo=visualstudio&logoColor=white)
![Focus](https://img.shields.io/badge/Focus-Data%20Structures-39424E)

4D Notepad is a data structures project that explores how an interactive text-editing application can be constructed using **pointer-based storage instead of a conventional text buffer**. Words are held in dynamically allocated nodes, connected in the order they were entered, and presented through a numbered, menu-driven Windows console interface. A separately implemented array-backed queue supports selecting and copying words from the document.

The name **“4D”** refers to the four directional links available in each text node: `next`, `prev`, `up`, and `down`. These represent navigational relationships within a custom linked structure; the project does not require four-dimensional coordinates or a graphical editing canvas.

## Project overview

The application treats a document as an ordered collection of **word nodes**. Instead of storing a complete passage in a single string, it reads a line from the console, separates it into space-delimited tokens, and creates a node for each token. Every node contains the token's text and pointers to neighboring nodes.

The principal linked-list traversal proceeds through `next` pointers, while `prev` pointers maintain a relationship with the preceding node. The program also establishes `up` and `down` connections between nodes separated by five positions in the sequence. This four-link layout provides an opportunity to study relationships beyond a traditional doubly linked list.

A numbered display allows users to identify words by position. Those positions are used by editing operations such as deletion and by the copying workflow. The queue holds copied words in the order selected, demonstrating how a second data structure can interact with the main text representation.

The interface is intentionally console-oriented: large ASCII headings, colored prompts, numeric menus, and text-based output keep the emphasis on core data-structure behavior rather than graphical interface frameworks.

### Technology stack

| Area | Technology | Purpose |
| --- | --- | --- |
| Language | C++ | Application logic and custom data structures |
| Text representation | Custom linked nodes | Store and traverse words |
| Copy buffer | Custom array-backed FIFO queue | Collect selected words in order |
| Console interface | Standard input/output | Read commands and display text |
| Windows integration | `windows.h` | Console text colors, delays, and console handle |
| Project system | Visual Studio C++ solution | Compile and run Debug or Release builds |
| Build toolset | MSVC v142 | Toolset configured in the project file |

## Features

### Interactive text entry

Choose **Open notepad** to enter text in the console. The program accepts input line by line and passes each line to a tokenization function. Words are appended to the linked list in entry order. To finish the input session and return to the menu, enter a tilde (`~`) on its own line.

### Positional word deletion

The delete operation displays the current document together with its numbered words. Enter a position to identify the word to remove from the forward-linked sequence. The deletion routine locates that position by traversing from the head and adjusts the relevant forward connection.

Positions are **one-based** in the user interface. For example, the first word is displayed with index `1`, the second with index `2`, and so on.

### Selective word copying

The copy workflow lets users select a number of words and then identify each one using its displayed index. A custom queue stores the selected strings and displays them in their queued order.

Unlike ordinary contiguous text selection, the program asks for individual word positions.

### Document display and indexing

**View notepad** traverses the linked sequence from the head and prints each stored word. While rendering, the program assigns a sequential `key` to each visited node and displays the key beside the word in a contrasting console color.

### Word counting

**Count total words** performs a forward traversal and increments a counter for each visited node. The result reflects the current number of stored tokens. Because input is separated on literal spaces, this count is specifically a **node/token count**, rather than the result of a language-aware word-segmentation system.

### Menu-driven Windows interface

Windows console formatting presents numbered commands and colored headings, while pause and clear-screen operations separate editing tasks.

## Data structures and design

### Four-link node representation

The `node` class is the central building block. Each instance represents one stored text token and exposes the following members:

| Member | Type | Role |
| --- | --- | --- |
| `key` | `int` | Position assigned when the notepad is displayed |
| `data` | `std::string` | Word or token stored in the node |
| `next` | `node*` | Link to the following token |
| `prev` | `node*` | Link to the preceding token |
| `up` | `node*` | Link toward an earlier row-like position |
| `down` | `node*` | Link toward a later row-like position |

The constructor initializes directional pointers to null. When inserting a token, the program either assigns the new node as `head` or traverses to the tail, attaches the node through `next`, and sets its `prev` pointer.

After the sequence exceeds five nodes, the insertion routine establishes additional vertical connections. In the source, it starts a second pointer five nodes ahead of `head` and links corresponding nodes using `down` and `up`. The primary operations displayed to the user traverse the sequence through `next` pointers. The four-link model is an instructional structure for examining multiple pointer relationships within the same node type.

### Custom queue for copied text

The `Queue` class provides a second storage mechanism using a dynamically allocated array of strings. It maintains `front`, `rear`, and `size` members to track queued content.

| Operation | Method | Description |
| --- | --- | --- |
| Allocate storage | `setSize(int n)` | Allocate room for a selected number of strings |
| Check capacity | `isFull()` | Determine whether the queue has reached its configured limit |
| Check emptiness | `isEmpty()` | Determine whether queued content is available |
| Enqueue selection | `enqueue_copy_txt3(int x)` | Find a word by position and append its text to the queue |
| Dequeue | `dequeue_paste_txt4()` | Remove and return the next queued string |
| Display contents | `displayQueue()` | Print the queued strings in sequence |

## Text-processing workflow

The internal workflow follows a small sequence of coordinated operations:

```text
User selects Open notepad
          |
          v
  Read one input line
          |
          v
  split(line) into tokens
          |
          v
 Create one node per token
          |
          v
 Append using next / prev
          |
          v
 Update up / down links
          |
          v
 Display, count, delete,
 or copy selected words
```

### Input parsing

The `split(string sentence)` function iterates over a line character by character. A literal space ends the current token and triggers insertion. The final token is inserted after the loop. A token equal to `~` is excluded from insertion, and the main input loop terminates when the **entire input line** is `~`.

For predictable results, use single spaces between words and type `~` alone when finishing input. The source processes spaces as separators and does not implement general-purpose punctuation normalization or natural-language tokenization.

### Position-based operations

Node positions are derived from a linear traversal rather than stored as permanent identifiers. The view function assigns sequential keys, while deletion and copying locate nodes by walking forward from `head`.

## Core functions

The implementation is contained in a single C++ source file, making the relationship between the data structures and user interface straightforward to inspect.

| Symbol | Responsibility |
| --- | --- |
| `node` | Define a text element with four directional links |
| `Queue` | Manage an array-backed FIFO collection of copied strings |
| `open_insert_txt_1()` | Append a token and establish linked relationships |
| `delete_txt_2()` | Remove an element at a chosen position in the forward sequence |
| `split()` | Separate an input line into space-delimited tokens |
| `count_total_Words_8()` | Count nodes reachable from `head` |
| `view_print_txt_9()` | Print words with sequential numeric labels |
| `Queue::enqueue_copy_txt3()` | Locate and queue the word at a selected position |
| `Queue::dequeue_paste_txt4()` | Return and remove the next queued word |
| `Queue::displayQueue()` | Show the current queue contents |
| `print_Heading()` | Render the main ASCII-art title |
| `main()` | Display menus, collect input, and coordinate operations |

## Build and run

### Prerequisites

- A **Windows** machine or compatible Windows development environment.
- **Visual Studio 2019** with the **Desktop development with C++** workload, or a newer Visual Studio installation with the **MSVC v142** toolset available.
- The Windows 10 SDK used by the solution configuration.

The project uses C++ standard-library headers plus Windows-specific headers. No third-party packages, external services, databases, or API credentials are required.

### Option 1 — Visual Studio

1. Clone the repository:

   ```powershell
   git clone https://github.com/SaadARazzaq/4D-Notepad-Data-Structures-Project.git
   ```

2. Open the solution file:

   ```text
   Source Code/4dnitepad.sln
   ```

3. Select a solution configuration such as **Debug | x64** or **Release | x64**.
4. Build the solution with **Build > Build Solution**.
5. Start the console application with **Ctrl+F5** to run without the debugger.

### Option 2 — MSVC developer command prompt

From a **Developer Command Prompt for Visual Studio**, compile the source directly:

```bat
cl /EHsc /std:c++17 "Source Code\4dnitepad\Source.cpp" /Fe:4DNotepad.exe
4DNotepad.exe
```

This alternative uses the same source file without loading the `.sln` project. Run the executable from a Windows console so Windows-specific color and screen commands behave as intended.

## Using the notepad

After launching the program, the main menu provides access to the text-related operations. The most useful workflow is to enter a passage, view its indexed words, and then experiment with the list and queue.

### Example session

1. Choose **1 — Open notepad**.
2. Enter a short line, such as `Data structures organize information`.
3. Enter `~` on its own line to return to the menu.
4. Choose **9 — View notepad** to see the stored words and their indices.
5. Choose **8 — Count total words** to count the stored nodes.
6. Choose **3 — Copy text**, specify how many words to select, and enter their displayed key values.
7. Choose **2 — Delete text** to remove a selected position from the linked sequence.
8. Choose **0 — Exit notepad** when finished.

### Interface reference

| Menu number | Label | Interaction |
| --- | --- | --- |
| `0` | Exit notepad | Close the application |
| `1` | Open notepad | Enter text until a line containing `~` |
| `2` | Delete text | Choose a numbered word position |
| `3` | Copy text | Specify a count and individual word positions |
| `8` | Count total words | Print the number of stored nodes |
| `9` | View notepad | Display indexed text in the console |

## Implementation details

### Traversal and operation costs

The program uses a linked representation, so several user-facing actions depend on the number of stored tokens. The following table summarizes the main operations conceptually.

| Operation | Typical work | Reason |
| --- | --- | --- |
| View all words | Linear traversal | Each node must be visited for output |
| Count words | Linear traversal | The function counts nodes from `head` |
| Append a word | Linear traversal | The implementation walks to the tail before attaching the node |
| Find a word by position | Linear traversal | Nodes are reached by following `next` pointers |
| Delete by position | Linear traversal | The target must first be located |
| Enqueue a selected string | Constant-time queue insertion after lookup | `rear` identifies the next array slot |
| Dequeue a string | Constant-time queue removal | `front` identifies the next item |

### Console presentation

The source uses `SetConsoleTextAttribute()` to distinguish prompts, headings, and numeric node labels. `Sleep()` and `system("pause")` provide simple timing and screen-control behavior, while `system("cls")` clears the current console view.

### What the project demonstrates

Taken together, the program provides a concrete exercise in dynamic allocation, pointer relationships, sequential traversal, position-based edits, FIFO buffering, text input processing, and coupling a data model to a terminal interface. Its key contribution is not a complex editing engine; it is the **direct, readable implementation of fundamental data structures inside an interactive program**.

## Repository structure

```text
4D-Notepad-Data-Structures-Project/
├── README.md
├── .gitattributes
└── Source Code/
    ├── 4dnitepad.sln                # Visual Studio solution
    ├── 4dnitepad/
    │   ├── Source.cpp              # Node, Queue, text logic, console UI
    │   ├── 4dnitepad.vcxproj        # C++ project configuration
    │   ├── 4dnitepad.vcxproj.filters
    │   └── Debug/                 # Committed build intermediates
    └── Debug/
        ├── 4dnitepad.exe           # Committed Windows executable
        └── 4dnitepad.pdb           # Debug symbols
```

The primary source of truth is [`Source.cpp`](Source%20Code/4dnitepad/Source.cpp). The solution and project files are provided for building the application in Visual Studio. The repository also contains previously generated debugging and executable artifacts.

## Author

**Saad Abdur Razzaq**  
GitHub: [@SaadARazzaq](https://github.com/SaadARazzaq)  
Repository: [4D-Notepad-Data-Structures-Project](https://github.com/SaadARazzaq/4D-Notepad-Data-Structures-Project)
