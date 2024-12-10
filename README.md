## 🌍 SafeWorld: Geo-Diverse Safety Alignment (NeurIPS 2024)

<div align="center">
<b><a href="https://wadeyin9712.github.io/">Da Yin</a>*, <a href="https://haoyiq114.github.io/">Haoyi Qiu</a>*</b>
<br>
<b><a href="https://khuangaf.github.io/">Kung-Hsiang Huang</a>, <a href="https://web.cs.ucla.edu/~kwchang/">Kai-Wei Chang</a>, <a href="https://violetpeng.github.io/">Nanyun Peng</a></b>
</div>
<br>
<div align="center">
University of California, Los Angeles
<br>
Salesforce AI Research
</div>
<div align="center">
<br>
<i>*Equal contribution, listed in alphabetical order by first name.</i>
</div>
<br>
<div align="center">
    <a href="https://arxiv.org/pdf/2412.06483"><img src="https://img.shields.io/badge/Paper-Arxiv-orange" ></a>
</div>

## Outlines

   - [🔍 Motivation for Geo-Diverse Safety in LLMs](#-motivation-for-geo-diverse-safety-in-llms)
   - [🧩 SafeWorld Benchmark](#-safeworld-benchmark)
   - [⚖️ Automatic Evaluation Framework](#%EF%B8%8F-automatic-evaluation-framework)
   - [🧨 Geo-Diverse Safety Alignment Training](#-geo-diverse-safety-alignment-training)


## 🔍 Motivation for Geo-Diverse Safety in LLMs


<p align="center">
    <img src="assets/intro.png?v=1&type=image"  width="90%;">
    <br>
    Figure 1: Examples of geo-diverse safety standards and the overall introduction of SafeWorld benchmark and its multi-dimensional evaluation.
</p>


* Large Language Models (LLMs), like LLaMA and GPT, are vital to many AI applications, serving millions globally.
* As LLMs become more widespread, concerns about their <span style="color: red;">**_safety_**</span> grow, with many studies now focusing on reducing their harmful impact. However, <span style="color: blue;">**_geo-diversity_**</span> remains an overlooked aspect.
* Addressing <span style="color: blue;">**_geographical variations_**</span> in safety principles is crucial, as <span style="color: orange;">**_cultural norms and legal standards_**</span> shape different definitions of safe and acceptable behavior.
* If a model overlooks cultural norms and local policies, it
risks causing conflicts among individuals or nations and
may lead to legal issues for local services.

➡️ To be equitable and effective, LLMs must <span style="color: red;">**_align_**</span> with diverse cultural and legal standards globally!


## 🧩 SafeWorld Benchmark


<p align="center">
    <img src="assets/datasets.png?v=1&type=image"  width="80%;">
    <br>
    Figure 2: The comparison between SafeWorld and other existing benchmarks.
</p>


* We introduce **_SafeWorld_**, the first <span style="color: red;">**_geo-diverse safety alignment_**</span> evaluation benchmark, focusing on <span style="color: blue;">**_cultural and legal safety_**</span>.
* It evaluates an LLM's ability to generate <span style="color: green;">**_helpful_**</span>, <span style="color: green;">**_safe_**</span>, and <span style="color: green;">**_appropriate_**</span> responses in a global context.
* Built from a global user survey, it includes **2.3k diverse queries**, simulating geo-diverse scenarios
validated to align with cultural-legal guidelines across **50 countries** and **439 regions/races**.

<p align="center">
    <img src="assets/generation_pipeline.png?v=1&type=image"  width="90%;">
    <br>
    Figure 3: Overview of queries generation pipeline.
</p>

Based on the survey insights, we have developed **four** distinct query types, each designed to elicit a specific type of response. Every query within SafeWorld presents a scenario that highlights a culturally or legally sensitive (or insensitive) situation, followed by a relevant question. The table below defines each query type:

| **Type**     | **Description** |
|:--------------:|---------------|
| `SpecificAnswer`    | Identifies the **specific** policy or cultural norm that was *violated* in the context of the queried country, race, or region. |
| `CompreAnswer`    | Provides a **comprehensive** explanation of the *violated* policies or cultural norms related to one or more countries, races, or regions.          |
| `RefuseToAnswer`  | **Avoids** addressing the query directly due to cultural or legal insensitivity. |
| `DoAnswer`   | Directly **addresses** the query because the query does not violate or show insensitivity towards any norm or policy. |


<p align="center">
    <img src="assets/queries.png?v=1&type=image"  width="90%;">
    <br>
    Figure 4: SafeWorld query examples across four types. Some are paired with their corresponding reference (i.e., ground-truth) cultural-legal guidelines.
</p>

You can find our evaluation benchmark under ```/data```:

```Text
data/
├── culture/
│   ├── type1.json
│   ├── type2.json
│   ├── type3.json
│   └── type4.json
│
└── policy/
    ├── type1.json
    ├── type2.json
    ├── type3.json
    └── type4.json
```

We divided the data into two separate folders, and the following table presents the statistics for each type of example in SafeWorld. For clarity, we have assigned the following labels: Type 1 corresponds to `SpecificAnswer`, Type 2 corresponds to `CompreAnswer`, Type 3 corresponds to `RefuseToAnswer`, and Type 4 corresponds to `DoAnswer`. Each number listed in the table indicates the count of examples for each respective type.

| Categories |  Type 1  |  Type 2  |  Type 3  |  Type 4  |
|------------|:--------:|:--------:|:--------:|:--------:|
| Cultural Norms   | 322    | 286      | 218   | 357     |
| Legal Policies   | 319    | 291      | 190   | 367     |

For each evaluation example in `type1.json` or `type4.json`, it consists of:

| **Name**     | **Description** |
|:--------------:|---------------|
| `index` | A unique identifier for each example, formatted as `{country}_{id1}_{id2}`, where `id1` is specific to the country and `id2` pertains to the region within that country if available; otherwise, this is set to empty. |
| `country` | The name of the country related to the evaluation example. |
| `region` | The specific region within the mentioned country. |
| `topic`  | The subject concerning the cultural norm or legal policy being evaluated. |
| `root_{norm/policy}` | The established norm or policy at the country level that applies nationwide. |
| `aug_{norm/policy}` | A region-specific norm or policy, if available; otherwise, this is set to null. |
| `scene` | A descriptive scenario illustrating where or how the norm or policy apply. |
| `specific_{norm/policy}` | A detailed explanation of how the norm or policy is relevant in the given scenario. |
| `query`| A composite of the scenario and a question, designed to probe understanding or application of the norm or policy.

For each evaluation example in `type2.json`, it consists of:

| **Name**     | **Description** |
|:--------------:|---------------|
| `country` | The name of the country related to the evaluation example. |
| `violated_speicic_{norm/policy}` | The specific norm or policy that is allegedly violated in the example. |
| `scene` | A descriptive scenario illustrating where or how the norm or policy apply. |
| `query`| A composite of the scenario and a question, designed to probe understanding or application of the norm or policy.

For each evaluation example in `type3.json`, it consists of:

| **Name**     | **Description** |
|:--------------:|---------------|
| `country` | The name of the country related to the evaluation example. |
| `query`| A composite of the scenario and a question, designed to probe understanding or application of the norm or policy.

## ⚖️ Automatic Evaluation Framework

* We assess LLM responses to geo-diverse safety queries using 3 automated protocols:
    * **Contextual appropriateness**, **accuracy**, and **comprehensiveness**.
* Our evaluation shows that <span style="color: blue;">**_LLaMA-_**</span> and <span style="color: orange;">**_Mistral-series_**</span> models can perform similarly to <span style="color: green;">**_GPT-3.5_**</span> and <span style="color: green;">**_GPT-4-turbo_**</span> across several metrics.
* Despite SafeWorld benchmark guidelines being derived from GPT-4-turbo, the model struggles with implicit queries and often underperforms compared to some open-source LLMs in response appropriateness.

➡️ This suggests that additional <span style="color: red;">**_alignment_**</span> methods may be necessary to effectively elicit and apply its learned knowledge in model responses!


<p align="center">
    <img src="assets/evaluation_framework.png?v=1&type=image"  width="90%;">
    <br>
    Figure 5: Overall framework of geo-diverse safety alignment training.
</p>

## 🧨 Geo-Diverse Safety Alignment Training

* Focusing on the widely used alignment method <span style="color: blue;">**_Direct Preference Optimization (DPO)_**</span>, we investigate how to synthesize training data for preference pairs that helps LLMs behave appropriately and accurately elicit factual knowledge.
* We synthesize training queries from human-verified cultural-legal guidelines. <span style="color: green;">**_Positive_**</span> responses align with these queries and guidelines. <span style="color: red;">**_Negative_**</span> responses are divided into:
    * **_Category 1_**: responses that reference guidelines correctly but inappropriately.
    * **_Category 2_**: responses that are behaviorally appropriate but incorrectly reference guidelines.

<p align="center">
    <img src="assets/alignment_training.png?v=1&type=image"  width="100%">
    <br>
    Figure 6: Overview of our multi-dimensional evaluation framework.
</p>

* Our <span style="color: orange;">**_SafeWorldLLM_**</span> model outperforms all competitors, including <span style="color: green;">**_GPT-4o_**</span>, across all three evaluated dimensions, along with a nearly 20% higher winning rate in helpfulness and harmfulness assessments by human evaluators from 9 countries.
* In addition, our **_SafeWorldAlign_** training data proves to be useful for maintaining performance on general NLP and safety evaluation tasks while enhancing geo-diverse safety alignment.

We provide the models' checkpoints in 🤗 Huggingface:

| **Model Names** | **🤗 Huggingface Links** |
|:--------------:|---------------|
| SafeWorldLM w/o Neg. Category 1 |  |
| SafeWorldLM w/o Neg. Category 2 |  |
| SafeWorldLM (50% Data) | |
| SafeWorldLM | |

<p align="center">
    <img src="assets/results_1.png?v=1&type=image"  width="75%">
    <img src="assets/results_2.png?v=1&type=image"  width="100%">
    <br>
    Table 1: Performance of different models on the SAFEWORLD benchmark.
</p>


## Citation
If you found this work useful, consider giving this repository a star and citing our paper as followed:

```bibtex
@inproceedings{yin-etal-2024-safeworld,
    title = "SafeWorld: Geo-Diverse Safety Alignment",
    author = "Yin, Da  and
              Qiu, Haoyi and
              Huang, Kung-Hsiang  and
              Chang, Kai-Wei  and
              Peng, Nanyun",
    year = "2024",
    publisher = "38th Conference on Neural Information Processing Systems (NeurIPS 2024)",
}
```
