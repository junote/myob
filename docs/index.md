# HelloWorld


Welcome to Foam-mkdocs-template! [[#support roamlinks]]!

* How to create this website and share your foam/obsidian notes? See: [[tutorial]]
* [foam](https://github.com/foambubble/foam)


## Support roamlinks

| origin                  | convert                             |
| ----------------------- | ----------------------------------- |
| `[Git Flow](git_flow.md)` | `[Git Flow](../software/git_flow.md)` |
| `[[Git Flow]]`            | `[Git Flow](../software/git_flow.md)` |
| `![[image.png]]`           | `![image.png](../image/imag.png)`      |
| `[[#Heading identifiers]]` | `[Heading identifiers in HTML](#heading-identifiers-in-html)`|
| `[[Git Flow#Heading]]`     |  `[Git Flow](../software/git_flow.md#heading)` |

## Usage：Deploy to github page

    1. fork this repository 
    2. add your documents to `docs` , `docs/index.md` is the main page of the website
    3. open `mkdocs.yml`, modify `site_name` to your website name, this file is the setting of website, visit link below to get more information
        * [mkdocs-material](https://squidfunk.github.io/mkdocs-material/)
        * [mkdocs](https://www.mkdocs.org/user-guide/configuration/)
    4. push to github, wait a moment, then visit `http://<your-github-username.github.io/<your-repo>`, for example:`jackiexiao.github.io/blog/`
    5. Done! That's all! Have fun!

    Thx to `Github Action`, it make deploy a blog so easy, all you need todo is modify and push your file

    ## Deploy Locally

    The simplest way: Enter your local repo directory, make sure your python > 3.6
    ```
    pip install mkdocs mkdocs-material mkdocs-roamlinks-plugin
    mkdocs serve 
    ```
    Then visit `http://127.0.0.1:8000/`
