
### install

```

sudo apt install apt-transport-https curl gnupg
curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor >bazel-archive-keyring.gpg
sudo mv bazel-archive-keyring.gpg /usr/share/keyrings
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/bazel-archive-keyring.gpg] https://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list

sudo apt update && sudo apt install bazel


```


### rules

[Bazel 教程：构建 C++ 项目](https://bazel.build/versions/6.4.0/start/cpp?hl=zh-cn)


查看 `cpp-tutorial/stage1/main` 目录中的 `BUILD` 文件：

```
cc_binary(
    name = "hello-world",
    srcs = ["hello-world.cc"],
)
```

在我们的示例中，`hello-world` 目标会实例化 Bazel 的内置 `[cc_binary rule](https://bazel.build/versions/6.4.0/reference/be/c-cpp?hl=zh-cn#cc_binary)`。该规则告诉 Bazel，从 `hello-world.cc` 个源文件，无依赖项。


```
cc_library(
    name = "hello-greet",
    srcs = ["hello-greet.cc"],
    hdrs = ["hello-greet.h"],
)

cc_binary(
    name = "hello-world",
    srcs = ["hello-world.cc"],
    deps = [
        ":hello-greet",
        "//lib:hello-time",
    ],
)
```

主软件包中的 `hello-world` 目标依赖于 `hello-time` 目标 在 `lib` 软件包中（因此是目标标签 `//lib:hello-time`）- Bazel 知道 通过 `deps` 属性实现这一目的。您可以在依赖项图中看到这一点：

为确保构建成功，请在 `lib/BUILD` 中创建 `//lib:hello-time` 目标 使用 visibility 属性对 `main/BUILD` 中的目标明确可见。 这是因为，在默认情况下，只有同一个 `BUILD` 文件。Bazel 使用目标可见性来防止库中包含的实现细节泄露到公共 API 中等问题。


