### 基本流程

1. **创建 GitHub 仓库**
    
    - 首先，在 GitHub 上创建一个新的仓库，用来存储你的 Obsidian 笔记库（Vault）。
    - 在仓库的设置中，建议将仓库设置为私有，以保护你的个人笔记内容不被公开访问。
2. **将 Obsidian 笔记库与 GitHub 仓库同步**
    
    - 在本地机器上，你可以通过 Git 将 Obsidian 的笔记文件夹与 GitHub 仓库关联，之后可以通过 Git 命令提交更改并推送到 GitHub。
    - 具体步骤：
        1. 进入你的 Obsidian 笔记库所在的文件夹。
        2. 使用 Git 初始化该文件夹：
            
            bash
            
            复制代码
            
            `git init`
            
        3. 添加远程仓库（替换为你自己的 GitHub 仓库链接）：
            
            bash
            
            复制代码
            
            `git remote add origin https://github.com/your-username/your-repo.git`
            
        4. 将文件添加到 Git 并提交：
            
            bash
            
            复制代码
            
            `git add . git commit -m "Initial commit" git push -u origin master`
            
3. **在其他设备上克隆仓库**
    
    - 在另一台设备上，你可以克隆这个 GitHub 仓库，将笔记下载到本地并同步更新：
        
        bash
        
        复制代码
        
        `git clone https://github.com/your-username/your-repo.git`
        
    - 然后，在 Obsidian 中将这个文件夹作为你的笔记库使用。
4. **日常同步与提交**
    
    - 在多台设备上工作时，每次修改完笔记后，都可以使用 Git 提交并推送到 GitHub：
        
        bash
        
        复制代码
        
        `git add . git commit -m "Update notes" git push`
        
    - 当你从另一台设备同步笔记时，可以先拉取远程仓库中的更新：
        
        bash
        
        复制代码
        
        `git pull`
        

### 使用插件进行自动化

为了简化这一过程，有些 Obsidian 插件可以帮助自动化 Git 的操作。例如：

- **Obsidian Git 插件**：这是一个非常流行的社区插件，可以让你在 Obsidian 内部直接进行 Git 的操作。它支持定时自动提交和推送、自动拉取更新等功能。配置好之后，插件可以定时帮你提交笔记的更新并推送到 GitHub，避免每次手动执行 Git 命令。
    - 安装方法：
        1. 在 Obsidian 中打开 "Settings" -> "Community plugins" -> "Browse"。
        2. 搜索并安装 "Obsidian Git" 插件。
        3. 配置插件的选项，比如自动同步间隔时间、提交消息模板等。

### 多设备同步提示

- **冲突管理**：当多个设备同时修改同一文件时，Git 可能会产生冲突。在这种情况下，你需要手动解决冲突。Obsidian Git 插件支持一些冲突解决策略，但如果你不熟悉 Git，建议先了解基本的冲突解决流程。
- **版本控制优势**：通过 GitHub，你可以方便地查看笔记的历史记录，回滚到某个历史版本，甚至可以创建分支来进行不同内容的实验性修改。

### 小技巧

- **.gitignore 文件**：有些 Obsidian 的缓存文件或插件数据不需要同步到 GitHub。你可以使用 `.gitignore` 文件忽略这些文件，比如：
    
    复制代码
    
    `.obsidian/ .DS_Store Thumbs.db`
    
- **Obsidian Sync**：Obsidian 也提供官方的云同步服务（Obsidian Sync），不过这是付费功能。如果你不想使用 GitHub 进行同步，可以考虑使用它。不过，GitHub 版本控制的优势在于你可以追踪历史变更，这是 Obsidian Sync 所不具备的。


### 如果有大文件
如果你有很多大文件（例如图片、视频或其他大容量数据），建议使用 [Git LFS](https://git-lfs.github.com/) 来处理大文件。Git LFS 允许你将大文件存储在 GitHub 上的一个独立位置，并避免将其直接推送到 Git 仓库中。

使用 Git LFS 的步骤：

1. 安装 Git LFS：
    
    bash
    
    复制代码
    
    `git lfs install`
    
2. 跟踪你希望使用 Git LFS 管理的文件类型，例如所有 `.png` 文件：
    
    bash
    
    复制代码
    
    `git lfs track "*.png"`
    
3. 添加并提交更改：
    
    bash
    
    复制代码
    
    `git add .gitattributes git add your-files git commit -m "Add files with Git LFS" git push origin master`
    

#### 5. 重试推送

有时只是临时性的问题，重新推送可能就能解决问题。你可以简单地再试一次：

bash

复制代码

`git push origin master`