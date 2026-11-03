# Reducing Source Bias in Fake News Detection

**A two-stage approach for human- and AI-written news**

Jiayi Ding · Jianyi (Nancy) Teng · Mahesh Nidhruva
Final project, DATASCI 266 (Natural Language Processing), UC Berkeley MIDS, Spring 2025

[Read the paper](paper/mitigating-bias-in-fake-news-detection.pdf)

## The problem

Fake news detectors are usually trained as a single classifier: real or fake. Once AI-generated text enters the mix, that design develops a blind spot. In our baselines, every model detected AI-written fake news far more reliably than human-written fake news. The models had learned the stylistic fingerprints of LLM output rather than the markers of deception itself.

We asked: does separating *who wrote it* from *is it true* reduce that bias?

## Approach

1. **Data.** A balanced four-class dataset (human-real, human-fake, AI-real, AI-fake) combining human-written articles from FakeNewsNet (GossipCop and PolitiFact) with LLM-generated news. 13,376 train, 1,672 validation, 1,676 test articles (80/10/10, stratified).
2. **Baselines.** Fine-tuned BERT, RoBERTa, DeBERTa and ModernBERT (base and large): batch size 16, learning rate 2e-5, 2 epochs, label smoothing 0.1, weight decay 0.01, early stopping on validation macro-F1.
3. **Bias analysis.** SHAP values, n-gram analysis, and robustness checks (typos, punctuation removal).
4. **Data-level fixes.** Length balancing, GPT-2 augmentation, and "humanizing" AI text.
5. **Model-level fixes.** Two two-stage architectures:
   - **Source-first:** classify human vs. AI, then route to a source-specific real/fake classifier.
   - **Veracity-first:** classify real vs. fake, then route to sub-models that identify the author type.

## Results

| Model | Overall accuracy | Macro-F1 |
|---|---|---|
| BERT-large | 0.84 | 0.846 |
| RoBERTa-large | 0.88 | 0.877 |
| DeBERTa-large | 0.88 | 0.878 |
| ModernBERT-large | 0.87 | 0.872 |

**The bias in one number:** ModernBERT-large reached 0.98 accuracy on AI-written fake news but 0.77 on human-written fake news.

**Two-stage results:** the source-first model reached about 86% accuracy and was strongest at catching AI-written fakes. The veracity-first model showed higher recall across categories but slightly underperformed on AI-written real news.

**What didn't work:** adding hand-built stylometric features (readability, sentiment, structure) to ModernBERT embeddings kept performance on AI-written text but slightly hurt human-written classes. The features added noise to already strong embeddings.

## Repository structure

    paper/                         corrected paper (PDF)
    slides/                        final presentation
    notebooks/baselines/           transformer baselines (Ding, Teng)
    notebooks/bias_analysis/       SHAP, n-gram, robustness, augmentation (Teng)
    notebooks/hybrid/              ModernBERT + stylometric features (Nidhruva)
    notebooks/two_stage/           source-first (Ding), veracity-first and ISOT test (Nidhruva)

Data is not included. See the paper for sources.

## Contributions

**Mahesh Nidhruva:** built the hybrid model; designed and built the veracity-first two-stage model; validated generalization on the ISOT dataset; wrote the abstract, introduction, background and the experiment sections for both models.

**Jiayi Ding:** built and evaluated the transformer baselines; designed and built the source-first two-stage model; wrote the dataset, baseline, architecture, results and conclusion sections.

**Jianyi (Nancy) Teng:** built baseline models; led the bias analysis (robustness, SHAP, n-grams); built the data augmentation and humanization; wrote the bias analysis and data enhancement sections.

## Limitations

The dataset covers political and entertainment news only. The models don't account for how misinformation tactics change over time.

## Acknowledgments

Completed for DATASCI 266 at the UC Berkeley School of Information.
