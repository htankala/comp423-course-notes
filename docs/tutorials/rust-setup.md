# Setting up a dev container for Rust

* Primary author: [Hamsini Tankala](https://github.com/htankala)
* Reviewer: [Arya Bharti](https://github.com/abharti-cmd)

#Rust Tutorial
##Prerequisites
Before we dive in, make sure you have:

1. A GitHub account: If you don’t have one yet, sign up at [GitHub](https://github.com/).
2. Git installed: [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) if you don’t already have it.
3. Visual Studio Code (VS Code): Download and install it from [here](https://code.visualstudio.com/).
4. Docker installed: Required to run the dev container. [Get Docker here](https://www.docker.com/products/docker-desktop).
5. Command-line basics: Your COMP211 command-line knowledge will serve you well here. If in doubt, review the Learn a CLI text! 

##Part 1. Project Setup: Creating the Repository
###Step 1. Create a Local Directory and Initialize Git

(A) Open your terminal or command prompt.


(B) Create a new directory for your project. (Note: Of course, if you'd like to organize this tutorial somewhere else on your machine, go ahead and change into that parent directory first. By default this will be in your user's home directory.):

```bash
mkdir comp423-course-notes
cd comp423-course-notes
```

(C) Initialize a new Git repository:

```bash
git init
```
!!! note "What is the effect of running the init subcommand?"
    You should know what happens when you run this command at this point in the course! If you do not, please refer back to the chapter on [Fundamental git Subcommands](https://comp423-25s.github.io/resources/git/ch2-git-fundamental-subcommands/).

(D) Create a README file:

```bash
echo "# COMP423 Course Notes" > README.md
git add README.md
git commit -m "Initial commit with README"
```
###Step 2. Create a Remote Repository on GitHub
(1) Log in to your GitHub account and navigate to the [Create a New Repository](https://github.com/new) page.

(2) Fill in the details as follows:

* **Repository Name:** <mark>comp423-course-notes</mark>
* **Description:** "Course notes organized as a static website using Material for MkDocs."
* **Visibility:** Public

(3) Do not initialize the repository with a README, .gitignore, or license.

(4) Click **Create Repository**

###Step 3. Link your Local Repository to GitHub

(1) Add the GitHub repository as a remote:

```bash
git remote add origin https://github.com/<your-username>/comp423-course-notes.git
```
Replace <mark>your-username</mark> with your GitHub username.

(2) Check your default branch name with the subcommand <mark>git branch</mark>. If it's not <mark>main</mark>, rename it to main with the following command: <mark>git branch -M main.</mark> Old versions of <mark>git</mark> choose the name <mark>master</mark> for the primary branch, but these days <mark>main</mark> is the standard primary branch name.

(3) Push your local commits to the GitHub repository:

```bash
git push --set-upstream origin main
```
!!! warning "Understanding the --set-upstream Flag"
    * <mark>git push --set-upstream origin main:</mark> This command pushes the main branch to the remote repository origin. The <mark>--set-upstream</mark> flag sets up the main branch to track the remote branch, meaning future pushes and pulls can be done without specifying the branch name and just writing <mark>git push origin</mark> when working on your local main branch. This long flag has a corresponding <mark>-u</mark> short flag.

(4) Back in your web browser, refresh your GitHub repository to see that the same commit you made locally has now been pushed to remote. You can use <mark>git log</mark> locally to see the commit ID and message which should match the ID of the most recent commit on GitHub. This is the result of pushing your changes to your remote repository.

##Part 2. Setting Up the Development Environment
###What is a Development (Dev) Container?
A dev container ensures that your development environment is consistent and works across different machines. At its core, a dev container is a preconfigured environment defined by a set of files, typically leveraging Docker to create isolated, consistent setups for development. Think of it as a "mini computer" inside your computer that includes everything you need to work on a specific project—like the right programming language, tools, libraries, and dependencies.

Why is this valuable? In the technology industry, teams often work on complex projects that require a specific set of tools and dependencies to function correctly. Without a dev container, each developer must manually set up their environment, leading to errors, wasted time, and inconsistencies. With a dev container, everyone works in an identical environment, reducing bugs caused by "it works on my machine" issues. It also simplifies onboarding new team members since they can start coding with just a few steps.

###How are software dependencies managed?
To effectively manage **software dependencies**, it's important to understand package and dependency management. In most software projects, you rely on external libraries or packages to save time and leverage work that has already been done by others. Managing these dependencies ensures that your project has access to the correct versions of these libraries, avoiding compatibility issues.

In this project, our primary dependency is <mark>mkdocs-material</mark>, which enables us to build and style our static site. This package is available on [PyPi](https://pypi.org/project/mkdocs-material/), the Python Package Index, which is a repository of software for the Python programming language. PyPi hosts thousands of free, open source, third-party libraries that developers can use to add functionality to their projects. These libraries are installed using <mark>pip</mark>, a package manager for Python. Similar tools and repositories exist for other programming languages—like <mark>npm</mark> for JavaScript, <mark>cargo</mark> for Rust, or <mark>maven</mark> for Java.

To ensure your dependencies are always correctly installed and available, in standard Python projects relying on <mark>pip</mark>, requirements are traditionally listed out in a <mark>requirements.txt</mark> file in the project's root directory. This file is committed to your project's version control history so that as your project adds or updates dependencies, it is reflected in the project's history. This allows anyone working on the project to quickly set up their environment by installing the necessary dependencies with the <mark>pip install</mark> command. The dev container configuration you setup will automatically install dependencies from <mark>requirements.txt</mark> when the container is created. This allows anyone working on the project to have a complete environment setup in one step: starting a dev container.

In summary, the the <mark>devcontainer.json</mark> file specifies configuration for a consistent development environment using a Docker image. The <mark>requirements.txt</mark> file ensures all needed Python package for our project are installed when the container is created. Together, these files automate the process of setting up a developer environment, making it easier for you and others to work on the project.

Lets establish your static website development environment:

###Step 1. Add Development Container Configuration

1. In VS Code, open the <mark>comp423-course-notes</mark> directory. You can do this via: File > Open Folder.
2. Install the **Dev Containers** extension for VS Code.
3. Create a <mark>.devcontainer</mark> directory in the root of your project with the following file inside of this "hidden" configuration directory:

<mark>.devcontainer/devcontainer.json</mark>

The <mark>devcontainer.json</mark> file defines the configuration for your development environment. Here, we're specifying the following:

* **name:** A descriptive name for your dev container.
* **image:** The Docker image to use, in this case, the latest version of a Python environment. Microsoft maintains a collection of base images for many programming language environments, but you can also create your own!
* **customizations:** Adds useful configurations to VS Code, like installing the Python extension. When you search for VSCode extensions on the marketplace, you will find the string identifier of each extension in its sidebar. Adding extensions here ensures other developers on your project have them installed in their dev containers automatically.
* **postCreateCommand:** A command to run after the container is created. Verifies that rust is correctly installed.

```bash
{
  "name": "Rust Dev Container",
  "image": "mcr.microsoft.com/devcontainers/rust:1-ubuntu-20.04",
  "settings": {
    "terminal.integrated.defaultProfile.linux": "bash"
  },
  "extensions": [
    "rust-lang.rust-analyzer"
  ],
  "postCreateCommand": "rustc --version"
}
```

###Step 3. Reopen the Project in a VSCode Dev Container
Reopen the project in the container by pressing Ctrl+Shift+P (or Cmd+Shift+P on Mac), typing "Dev Containers: Reopen in Container," and selecting the option. This may take a few minutes while the image is downloaded and the requirements are installed.

Once your dev container setup completes, close the current terminal tab (trash can), open a new terminal pane within VSCode, and try running python --version to see your dev container is running a recent version of Python without much effort! (As of this writing: 3.13 released in October of 2024.)

##Part 3: Initialize a new Rust project
###Step 1: Inside the dev container, open a terminal and run the following commands:
```bash
cargo new hello_comp423_rust --vcs none
cd hello_comp423_rust
```
!!! note "The --vcs none flag prevents cargo from automatically initializing a Git repository."

###Step 2: Open the <mark>src/main.rs</mark> and add the following code:
```bash
fn main() {
    println!("Hello COMP423");
}
```

##Part 4: Compile and Run the Program
###Step 1: To compile the code, run the following command:
```bash
cargo build
```
!!! note "The cargo build command is similar to using gcc in COMP211. It only compiles the code but won't run the code."

###Step 2: To run the built file, use the following command from the [target folder](https://doc.rust-lang.org/cargo/commands/cargo-build.html#output-options), which will be present in the root project directory:
```bash
./hello_comp423_rust
```
To compile and run the program in one step, use the following command:
```bash
cargo run
```
!!!note "Cargo build is used only for building, while the cargo run command will compile the program and automatically execute it."


