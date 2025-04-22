中文版：
# Twitter Entity Sentiment Analysis: LIME 稳定性分析实验报告

## 📁 项目简介
本项目旨在评估 **LIME (Local Interpretable Model-agnostic Explanations)** 在 Twitter 实体情感分类任务中的解释稳定性（robustness）。我们基于 Kaggle 的公开数据集，训练了一个情感分类模型，并逐步构建了解释器、扰动器、评估器与可视化模块，最终形成系统性实验框架。

---

## 📦 项目结构与模块说明
| 文件名              | 模块功能                             |
|---------------------|--------------------------------------|
| `data.qmd`          | 数据加载与探索                       |
| `modeling.qmd`      | 构建分类模型与解释器                 |
| `lime.qmd`          | 对文本进行 LIME 解释                 |
| `perturbation.qmd`  | 构造扰动样本并评估解释稳定性         |
| `visualization.qmd` | 可视化扰动前后的解释差异与稳定性分布 |
| `conclusion.qmd`    | 总结实验发现与未来工作建议           |

---

## 🔍 1. 数据探索 (`data.qmd`)
- 使用 `pandas` 读取训练/验证数据集，人工添加列名（id, entity, sentiment, tweet）
- 缺失值分析：发现训练集中 tweet 字段缺失 686 条
- 情感分布分析：使用 `seaborn.countplot` 可视化训练集和验证集中的标签分布（positive / neutral / negative）

---

## 🛠️ 2. 模型训练与解释器构建 (`modeling.qmd`)
- 使用 `TfidfVectorizer + LogisticRegression` 构建 sklearn pipeline
- 训练完成后构建 `LimeTextExplainer(class_names=...)`，用于解释每条 tweet 的模型预测输出

---

## 🧠 3. 生成解释 (`lime.qmd`)
- 随机抽取验证集中一条 tweet 文本，获取其 LIME 解释结果（使用 `explain_instance`）
- 使用 `HTML(explanation.as_html())` + `IPython.display.display` 进行 notebook 内嵌展示
- 输出关键词和对应的权重，展示模型是如何做出该预测的

---

## 🔄 4. 稳定性分析：文本扰动 (`perturbation.qmd`)
- 构造随机扰动函数：
  - `random_deletion(text, p)`：以概率 `p` 删除词
  - `random_insertion(text, p)`：以概率 `p` 插入词
- 针对某条 tweet 生成多个扰动版本
- 每个扰动文本重新调用 `explainer.explain_instance()`
- 使用 Jaccard 相似度比较原始解释与扰动解释的关键词重叠程度：
  ```python
  def jaccard(set1, set2):
      return len(set1 & set2) / len(set1 | set2)
  ```
- 最终输出稳定性得分列表：如 `[0.9, 0.8, 0.6, 1.0, 0.8]`

---

## 📈 5. 可视化分析 (`visualization.qmd`)
- 可视化原始解释与 perturbation 的关键词条形图对比
- 使用 `matplotlib` 绘制稳定性得分的柱状图
- 分析各扰动样本下解释的一致性程度是否显著下降

---

## 🧾 6. 总结与启示 (`conclusion.qmd`)
- LIME 可以有效突出关键词汇，有助于理解模型预测
- 但其对文本的微小变动敏感（如删除某个词可能显著改变解释）
- 稳定性问题提示我们在实际部署时需谨慎使用

**改进方向：**
- 加入 SHAP、Anchors 等方法做对比
- 使用更复杂模型（如 BERT）构建分类器
- 使用 counterfactual 生成或 adversarial 训练增强鲁棒性

---

## 🧪 依赖环境与 requirements.txt（建议）
```txt
pandas
numpy
matplotlib
seaborn
scikit-learn
lime
ipython
```
一键安装所有依赖：
```bash
pip install -r requirements.txt
```

---

## 🚀 零基础使用本项目（GitHub）

### ✅ 1. 克隆项目
```bash
git clone https://github.com/你的用户名/lime-nlp-stability.git
cd lime-nlp-stability
```

### ✅ 2. 安装 Python 环境与依赖
建议使用虚拟环境：
```bash
python -m venv .venv
source .venv/bin/activate       # Linux/macOS
.venv\Scripts\activate          # Windows
pip install -r requirements.txt
```

### ✅ 3. 在 RStudio 或 VSCode 中运行 Quarto 项目
```bash
quarto preview
```
或直接在 RStudio 中点击 `Render All` 生成 HTML 报告。

你将获得一个多页面的解释性分析站点，模块清晰，结构完善。

---
 网页版浏览:  
   [https://rain-shi.github.io/lime-nlp-stability](https://rain-shi.github.io/lime-nlp-stability)

English Version：
 **Twitter Entity Sentiment Analysis with LIME** 
---

```markdown
# 🧠 Explaining Text Classification Models with LIME  
_A study on Stability and Interpretability in NLP_

[![Quarto Book](https://img.shields.io/badge/Quarto-Book-blue?logo=quarto)](https://rain-shi.github.io/lime-nlp-stability/)

## 📘 Project Overview

This project explores **local model interpretability** using **LIME** (Local Interpretable Model-Agnostic Explanations) on the [Twitter Entity Sentiment Analysis](https://www.kaggle.com/competitions/twitter-entity-sentiment-analysis) dataset.

We aim to answer:
- How does LIME explain predictions made by text classifiers?
- How stable are explanations under small input perturbations?

---

## 📁 Dataset

The dataset consists of two CSV files:

- `twitter_training.csv`: Main training set (~74k records)
- `twitter_validation.csv`: Validation set (~1k records)

Each row includes:
- `id`: Identifier
- `entity`: Entity name (e.g. company, person)
- `sentiment`: One of `Positive`, `Neutral`, `Negative`
- `tweet`: The full tweet text

---

## 🛠 Tech Stack

- **Python 3.11**
- **pandas**, **scikit-learn**, **lime**
- **Seaborn / Matplotlib** for visualization
- **Quarto + RStudio** for publishing interactive analysis
- **Git + GitHub** for version control
- **Virtual environment** managed via `.venv/`

---

## 🧪 Key Files

| File                | Description |
|---------------------|-------------|
| `data.qmd`          | Dataset loading, cleaning, and summary statistics |
| `modeling.qmd`      | Text classification model and evaluation |
| `lime.qmd`          | LIME explanation and visualization |
| `perturbation.qmd`  | Analysis of explanation stability |
| `visualization.qmd` | Custom plots and distributions |
| `conclusion.qmd`    | Final remarks and findings |
| `_quarto.yml`       | Quarto navigation and configuration |
| `data/`             | Contains the CSV files |
| `docs/`             | Rendered HTML outputs for deployment |

---

## 🚀 How to Reproduce

1. Clone this repo  
   ```bash
   git clone https://github.com/Rain-Shi/lime-nlp-stability.git
   cd lime-nlp-stability
   ```

2. Create and activate a virtual environment  
   ```bash
   python -m venv .venv
   .\.venv\Scripts\activate  # on Windows
   ```

3. Install dependencies  
   ```bash
   pip install -r requirements.txt  # or install manually
   ```

4. Launch RStudio or VSCode with Quarto and run  
   ```bash
   quarto render
   ```

5. Open in browser (if deployed):  
   [https://rain-shi.github.io/lime-nlp-stability](https://rain-shi.github.io/lime-nlp-stability)

---

## 📌 Status

✅ Data cleaned  
✅ Model trained  
✅ LIME applied  
✅ Git conflict resolved  
🚧 Explanation stability in progress  
🚧 Final evaluation coming next...

---

## 🤝 Acknowledgements

- [LIME by Ribeiro et al.](https://arxiv.org/abs/1602.04938)  
- [Quarto for publishing](https://quarto.org/)  
- [Twitter dataset on Kaggle](https://www.kaggle.com/competitions/twitter-entity-sentiment-analysis)

---

## 📬 Contact

Feel free to reach out by [opening an issue](https://github.com/Rain-Shi/lime-nlp-stability/issues) or connecting via GitHub.

---

_This repo was built as part of the final project for EDAV @ Columbia._
```

---


## Follow these instructions carefully

*If you have any difficulties or have feedback of any kind, please [file an issue](https://github.com/jtr13/quarto-edav-template/issues) or ask questions in the [Discussions](https://github.com/jtr13/quarto-edav-template/discussions) section.*

[Video tutorial](https://www.youtube.com/watch?v=emgS2JI4jCk) (walkthrough of steps below)

### Copy this template (GitHub)

- [ ] 1. Click the green "Use this template" button above and choose "Create a new repository". If you don't see the "Use this template" option, **log in to GitHub**. DO NOT FORK THE REPO. Choose a descriptive name for your repo, such as "federalbudget" or "AIDSdeaths". (If you change your topic before you do any work, delete the repo and start over.)

- [ ] 2. Leave the setting for viewing the repo as "Public". (Otherwise, we will not be able to access your rendered book.)

- [ ] 3. In the Description field, write "Source files for final project" then click "Create repository".

### Set up Pages (GitHub)

- [ ] 1. You've now left the template page and are viewing your new repo on GitHub. On the home page, click Settings. Click the "Pages" section on the left. In the Build and Deployment section, set Source to "Deploy from a branch" (Classic Pages experience) and Branch to main with /docs folder. Click Save.

- [ ] 2. Click the little gear button near "About" on the top right side of the home page of the repo and check the "Use your Github Pages website" box under "Website". Click "Save changes". Test the link and you should see a web site with a stick figure on it. It may take a few minutes to build so if it's not working do a few more steps and then come back to check.

### Copy the repo link (GitHub)

- [ ] 1. Click the green Code button, choose "HTTPS" and copy the link below. It should have the format: https&#xfeff;://github.com/[USERNAME]/[REPONAME].git

### Clone the repo (RStudio)

- [ ] 1. Clone your new repo with *File, New Project..., Version Control, Git* in RStudio. You will need to paste the link from the previous step in the Repository URL box. If it's not automatically populated, enter the repo name in the "Project directory name:" box. Choose the location of the project.

### Edit `_quarto.yml` (RStudio)

Tip: From the file pane in RStudio, open `README.md`, which contains these instructions. You can delete steps as you complete them.

- [ ] 1. Change the all caps info in the `title:`, `author:` and `repo-url` fields in the YAML (top) section of `_quarto.yml` to your info. (Note: it's very important to maintain the indenting structure in this file precisely as is -- be careful!)

### Render the book (RStudio)

- [ ] 1. If you haven't already, click "Help" "Check for Updates" to make sure you have the latest version of RStudio (and thus have Quarto installed.)

- [ ] 2. Render the web site locally by clicking the "Build" tap on the right and then "Render Book".

- [ ] 3. Use `browseURL("docs/index.html")` to view your book locally (or just open `docs/index.html` in a browser).

- [ ] 4. If it looks good, commit and push all changed files to GitHub. 

(You will need to repeat steps 2 and 4 every time you wish to update the book on GitHub Pages.)

### Update README (GitHub or RStudio)

- [ ] 1. Delete the content of this **README** and add a short description of your project in its place. If you're working locally, be sure to commit and push the changes to GitHub.

### Optional

- [ ] 1. Choose a theme from [https://bootswatch.com/](https://bootswatch.com/) and replace "cosmo" in `_quarto.yml` with your prefered theme.

### Additional features

Please consult the official guide to **quarto** book websites: [https://quarto.org/docs/books/](https://quarto.org/docs/books/)



