# 访客 API

* 除非特别说明，所有 HTTP 请求均使用 POST。
* 所有的大于 400 的返回值，都应当按照请求失败处理。

## 列出评论

地址：`/v3/list`

### 提交参数

为 JSON 格式，具体信息如下：

| 参数名 | 类型 | 必需 | 说明 |
| - | - | - | - |
| `url` | `string` | 是 | 评论串所对应的页面地址 |

### 返回参数

为 JSON 格式，具体信息如下：

| 参数名 | 类型 | 说明 |
| - | - | - |
| `url` | `string` | 评论串所对应的页面地址 |
| `locked` | `boolean` | 评论是否被锁定 |
| `content` | `array` | 评论串内容 |
| `content[]/uuid` | `integer` | 评论在数据库中的 UUID |
| `content[]/name` | `string` 或 `null` | 评论者的昵称。如果昵称为 `null`，则该用户进行了匿名发言，同时 `website` 与 `email_hashed` 将始终是 `null`。 |
| `content[]/emailHashed` | `string` 或 `null` | 评论者留下的电子邮箱地址的 MD5 散列。该值用于在评论列表中展示评论者的 [Gravatar](https://gravatar.com/) 头像 |
| `content[]/website` | `string` 或 `null` | 评论者留下的个人主页地址 |
| `content[]/avatar` | `string` 或 `null` | 访客的头像 URL 地址。如果值不为 null，则优先展示该值指定的头像。这是一个**不允许**访客自行指定的值，该字段存在的目的是为了更好的处理**从其它评论系统导入的评论**所**附带**的访客头像。 |
| `content[]/parent` | `string` 或 `null` | 该评论回复的已有的其它评论的 UUID。如果没有回复已有的其它评论，则为 `null` |
| `content[]/content` | `string` | 评论内容 |
| `content[]/byAdmin` | `boolean` | 是否为管理员发布 |
| `content[]/createdAt` | `integer` | 评论发布的日期。以 Java 时间戳表示 |
| `content[]/edited` | `boolean` | 评论是否在发布后被编辑过 |

如果数据库中没有所对应的评论串，则返回 `content` 为空，`name` 为用户提交的 `url` 的值，`locked` 为 `false` 的返回值。

## 提交评论

地址：`/v3/submit`

为 JSON 格式，具体信息如下：

| 参数名 | 类型 | 必需 | 说明 | 最大 UTF-8 字节数 |
| - | - | - | - | - |
| `title` | `string` | 是 | 要评论的文章的标题 | |
| `url` | `string` | 是 | 要评论的文章的 URL | |
| `parent` | `string` 或 `null` | 是 | 要回复的已有的评论 UUID。如果是发布一篇新评论，则应当提交 `null` | |
| `name` | `string` | 否 | 评论作者的昵称。如果不指定，则会被认为是匿名评论 | 32 |
| `email` | `string` | 是 | 评论作者的电子邮箱地址，用来展示 Gravatar 头像与接收提醒邮件（如果 `receiveEmail` 为 `true`）。<br>匿名评论也需要提交该参数，但其电子邮箱地址的 MD5 值将不会被公开输出。 | 255 |
| `website` | `string` | 否 | 评论作者的个人主页。<br>匿名评论可以提交该参数，但个人主页地址将不会被公开输出。 | 64 |
| `content` | `string` | 是 | 评论内容。 | 2048 |
| `receiveEmail` | `boolean` | 是 | 是否在评论得到回复时接收电子邮件提醒。 | |
| `responseKey` | `string` | 是 / 不需要 | reCAPTCHA v3 的验证结果密钥。<br>**如果** API 服务端指定开启 reCAPTCHA v3 验证，则必须提交有效值。 | |

### 返回参数

为 JSON 格式，具体信息如下：

| 参数名 | 类型 | 说明 |
| - | - | - |
| `uuid` |  `integer` | 评论在数据库中的 UUID |
| `name` |  `string` 或 `null` | 评论者的昵称。如果昵称为 `null`，则该用户进行了匿名发言 |
| `email` |  `string` | 评论者留下的电子邮箱地址 |
| `website` |  `string` 或 `null` | 评论者留下的个人主页地址 |
| `parent` |  `string` 或 `null` | 该评论回复的已有的其它评论的 UUID。如果没有回复已有的其它评论，则为 `null` |
| `content` |  `string` | 评论内容 |
| `editKey` |  `string` | 用于编辑或删除该评论的密钥 |
| `createdAt` |  `integer` | 评论发布的日期。以 Java 时间戳表示 |
| `updatedAt` |  `integer` | 评论最后编辑的日期。以 Java 时间戳表示 |
