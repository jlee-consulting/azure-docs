---
title: Submit batch run and evaluate a flow in prompt flow
titleSuffix: Azure Machine Learning
description: Learn how to submit batch run and use built-in evaluation methods in prompt flow to evaluate how well your flow performs with a large dataset with Azure Machine Learning studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: prompt-flow
ms.custom:
  - ignite-2023
  - build-2024
ms.topic: how-to
author: lgayhardt
ms.author: lagayhar
ms.reviewer: ziqiwang
ms.date: 11/06/2023
---

# Submit batch run and evaluate a flow

To evaluate how well your flow performs with a large dataset, you can submit batch run and use built-in evaluation methods in prompt flow.

In this article you learn to:

- Submit a Batch Run and Use a Built-in Evaluation Method
- View the evaluation result and metrics
- Start A New Round of Evaluation
- Check Batch Run History and Compare Metrics
- Understand the Built-in Evaluation Metrics
- Ways to Improve Flow Performance
- Further reading: Guidance for creating Golden Datasets used for Copilot quality assurance

You can quickly start testing and evaluating your flow by following this video tutorial [submit batch run and evaluate a flow video tutorial](https://www.youtube.com/watch?v=5Khu_zmYMZk).

## Prerequisites

To run a batch run and use an evaluation method, you need to have the following ready:
- A test dataset for batch run. Your dataset should be in one of these formats: `.csv`, `.tsv`, or `.jsonl`. Your data should also include headers that match the input names of your flow. Further Reading: If you're building your own copilot, we recommend referring to [Guidance for creating Golden Datasets used for Copilot quality assurance](#further-reading-guidance-for-creating-golden-datasets-used-for-copilot-quality-assurance).

## Submit a batch run and use a built-in evaluation method

A batch run allows you to run your flow with a large dataset and generate outputs for each data row. You can also choose an evaluation method to compare the output of your flow with certain criteria and goals. An evaluation method  **is a special type of flow**  that calculates metrics for your flow output based on different aspects. An evaluation run is executed to calculate the metrics when submitted with the batch run.

To start a batch run with evaluation, you can select on the **"Evaluate"** button on the top right corner of your flow page.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-button.png" alt-text="Screenshot of Web Classification with batch run highlighted. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-button.png":::

To submit batch run, you can select a dataset to test your flow with. You can also select an evaluation method to calculate metrics for your flow output. If you don't want to use an evaluation method, you can skip this step and run the batch run without calculating any metrics. You can also start a new round of evaluation later.

First, you're asked to give your batch run a descriptive and recognizable name. You can also write a description and add tags (key-value pairs) to your batch run. After you finish the configuration, select **"Next"** to continue.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-name.png" alt-text="Screenshot of batch run settings where you specify run name and description. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-name.png":::

Second, you need to select or upload a dataset that you want to test your flow with. 
Prompt flow also  supports mapping your flow input to a specific data column in your dataset. This means that you can assign a column to a certain input. You can assign a column to an input by referencing with `${data.XXX}` format. If you want to assign a constant value to an input, you can directly type in that value.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-setting.png" alt-text="Screenshot of batch run settings where you select a test dataset. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-setting.png":::

Then, in the next step, you can decide to use an evaluation method to validate the performance of this run either immediately or later. For a completed batch run, a new round of evaluation can still be added.

You can directly select the **"Next"** button to skip this step and run the batch run without using any evaluation method to calculate metrics. In this way, this batch run only generates outputs for your dataset. You can check the outputs manually or export them for further analysis with other methods.

Otherwise, if you want to run batch run with evaluation now, you can select one or more evaluation methods based on the description provided. You can select **"More detail"** button to see more information about the evaluation method, such as the metrics it generates and the connections and inputs it requires.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-selection.png" alt-text="Screenshot of evaluation settings where you can select built-in evaluation method." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-selection.png":::

Go to the next step and configure evaluation settings. In the  **"Evaluation input mapping"**  section, you need to specify the sources of the input data that are needed for the evaluation method. For example, ground truth column might come from a dataset. By default, evaluation uses the same dataset as the test dataset provided to the tested run. However, if the corresponding labels or target ground truth values are in a different dataset, you can easily switch to that one.  

Therefore, to run an evaluation, you need to indicate the sources of these required inputs. To do so, when submitting an evaluation, you see an  **"Evaluation input mapping"**  section.

- If the data source is from your run output, the source is indicated as **"${run.output.[OutputName]}"**
- If the data source is from your test dataset, the source is indicated as **"${data.[ColumnName]}"**

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-input-mapping.png" alt-text="Screenshot of evaluation input mapping." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-input-mapping.png":::

> [!NOTE]
> If your evaluation doesn't require data from the dataset, you do not need to reference any dataset columns in the input mapping section, indicating the dataset selection is an optional configuration. Dataset selection won't affect evaluation result.

If an evaluation method uses Large Language Models (LLMs) to measure the performance of the flow response, you're also required to set connections for the LLM nodes in the evaluation methods.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-connection.png" alt-text="Screenshot of connection where you can configure the connection for evaluation method. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-evaluation-connection.png":::

> [!NOTE]
> Some evaluation methods require GPT-4 or GPT-3 to run. You must provide valid connections for these evaluation methods before using them.
> Some evaluation process may take up lots of tokens, so it's recommended to use a model which can support >=16k tokens.

After you finish the input mapping, select on  **"Next"**  to review your settings and select on  **"Submit"**  to start the batch run with evaluation.

> [!NOTE]
> Batch runs have a maximum duration of 10 hours. If a batch run exceeds this limit, it will be terminated and marked as failed. We advise monitoring your Large Language Model (LLM) capacity to avoid throttling. If necessary, consider reducing the size of your data. If you continue to experience issues or need further assistance, don't hesitate to reach out to our product team through the feedback form or support request.

## View the evaluation result and metrics

After submission, you can find the submitted batch run in the run list tab in prompt flow page. 

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-list.png" alt-text="Screenshot of prompt flow run list page where you find batch runs. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-list.png":::

Select a run to navigate to the **Run result** page, to check the results of this batch run. 

### Output

#### Basic result and trace

This will firstly direct you to the **Output tab** to view the inputs and outputs line by line. The output tab page displays a table list of results, including the **line ID**, **input**, **output**, **status**, **system metrics**, and **created time**.

For each line, selecting **View trace** allows you to observe and debug that particular test case in its trace detailed page.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-output.png" alt-text="Screenshot of batch run result page on the outputs tab where you check batch run outputs. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-output.png":::

:::image type="content" source="./media/how-to-develop-flow/authoring-trace.png" alt-text=" Screenshot of view output button in two locations." lightbox ="./media/how-to-develop-flow/authoring-trace.png":::

#### Append evaluation result and trace

Selecting **Append evaluation output** allows you to select related evaluation runs and you see appended columns at the end of the table showing the evaluation result for each row of data. Multiple evaluation outputs can be appended for comparison.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-output-append-evaluation.png" alt-text="Screenshot of batch run outputs to append evaluation output. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-output-append-evaluation.png":::

You can see the **latest evaluation metrics** in the left **Overview** panel.

#### Essential overview

On the right side, the Overview offers overall information about the run, such as the number of per data point execution, total tokens, and duration of the run. 

The latest evaluation run aggregated metrics are shown here by default, you can select View evaluation run to jump to view the evaluation run itself. 

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-output-overview.png" alt-text="Screenshot of batch run overview information in output page. " lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-output-overview.png":::

The overview can be expanded and collapsed here, and you can select View full information, which will direct you to the [Overview tab](#overview) beside the Output tab, where is containing more detailed information of this run.

#### Start a new round of evaluation

If you have already completed a batch run, you can start another round of evaluation to submit a new evaluation run to calculate metrics for the outputs **without running your flow again**. This is helpful and can save your cost to rerun your flow when:

- you didn't select an evaluation method to calculate the metrics when submitting the batch run, and decide to do it now.
- you have already used evaluation method to calculate a metric. You can start another round of evaluation to calculate another metric.
- your evaluation run failed but your flow successfully generated outputs. You can submit your evaluation again.

You can select **Evaluate** to start another round of evaluation.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-output-new-evaluation.png" alt-text="Screenshot of batch run output page on where to start a new round of evaluation." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-output-new-evaluation.png":::

After setting up the configuration, you can select **"Submit"** for this new round of evaluation. After submission, you'll be able to see a new record in the prompt flow run list. After the evaluation run completed, similarly, you can check the result of evaluation in the **"Outputs"** tab of the batch run detail panel. You need to select the new evaluation run to view its result.

To learn more about the metrics calculated by the built-in evaluation methods, navigate to [understand the built-in evaluation metrics](#understand-the-built-in-evaluation-metrics).

### Overview

Selecting the **Overview tab** shows comprehensive information about the run, including run properties, input dataset, output dataset, tags, and description.

### Logs

Selecting the **Logs tab** allows you to view the run logs, which can be useful for detailed debugging of execution errors. You can download the log files to your local machine.

### Snapshot

Selecting the **Snapshot tab** shows you the run snapshot. You can view the DAG of your flow. Additionally, you have the option to **Clone** it to create a new flow. You can also **Deploy** it as an online endpoint.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-snapshot.png" alt-text="Screenshot of batch run snapshot." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-snapshot.png":::

## Check batch run history and compare metrics

In some scenarios, you modify your flow to improve its performance. You can submit multiple batches runs to compare the performance of your flow with different versions. You can also compare the metrics calculated by different evaluation methods to see which one is more suitable for your flow.

To check the batch run history of your flow, you can select the **"View batch run"** button on the top right corner of your flow page. You see a list of batch runs that you have submitted for this flow.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-history.png" alt-text="Screenshot of Web Classification with the view bulk runs button selected." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-history.png":::

You can select on each batch run to check the detail. You can also select multiple batch runs and select on the **"Visualize outputs"** to compare the metrics and the outputs of this batch runs.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-history-list.png" alt-text="Screenshot of batch run runs showing the history." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-history-list.png":::

In the "Visualize output" panel the **Runs & metrics** table shows the information of the selected runs with highlight. Other runs that take the outputs of the selected runs as input are also listed.

In the "Outputs" table, you can compare the selected batch runs by each line of sample. By selecting the "eye visualizing" icon in the "Runs & metrics" table, outputs of that run will be appended to the corresponding base run.

:::image type="content" source="./media/how-to-bulk-test-evaluate-flow/batch-run-compare.png" alt-text="Screenshot of metrics compare of multiple batch runs." lightbox = "./media/how-to-bulk-test-evaluate-flow/batch-run-compare.png":::

## Understand the built-in evaluation metrics

In prompt flow, we provide multiple built-in evaluation methods to help you measure the performance of your flow output. Each evaluation method calculates different metrics. Now we provide nine built-in evaluation methods available. You can check the following table for a quick reference:

| Evaluation Method | Metrics  | Description | Connection Required | Required Input  | Score Value |
|---|---|---|---|---|---|
| Classification Accuracy Evaluation | Accuracy | Measures the performance of a classification system by comparing its outputs to ground truth. | No | prediction, ground truth | in the range [0, 1]. |
| QnA Relevance Scores Pairwise Evaluation | Score, win/lose | Assesses the quality of answers generated by a question answering system. It involves assigning relevance scores to each answer based on how well it matches the user question, comparing different answers to a baseline answer, and aggregating the results to produce metrics such as averaged win rates and relevance scores. | Yes | question, answer (no ground truth or context)  | Score: 0-100, win/lose: 1/0 |
| QnA Groundedness Evaluation | Groundedness | Measures how grounded the model's predicted answers are in the input source. Even if LLM’s responses are true, if not verifiable against source, then is ungrounded.  | Yes | question, answer, context (no ground truth)  | 1 to 5, with 1 being the worst and 5 being the best. |
| QnA GPT Similarity Evaluation | GPT Similarity | Measures similarity between user-provided ground truth answers and the model predicted answer using GPT Model.  | Yes | question, answer, ground truth (context not needed)  | 1 to 5, with 1 being the worst and 5 being the best. |
| QnA Relevance Evaluation | Relevance | Measures how relevant the model's predicted answers are to the questions asked.  | Yes | question, answer, context (no ground truth)  | 1 to 5, with 1 being the worst and 5 being the best. |
| QnA Coherence Evaluation | Coherence  | Measures the quality of all sentences in a model's predicted answer and how they fit together naturally.  | Yes | question, answer (no ground truth or context)  | 1 to 5, with 1 being the worst and 5 being the best. |
| QnA Fluency Evaluation | Fluency  | Measures how grammatically and linguistically correct the model's predicted answer is.  | Yes | question, answer (no ground truth or context)  | 1 to 5, with 1 being the worst and 5 being the best |
| QnA f1 scores Evaluation | F1 score   | Measures the ratio of the number of shared words between the model prediction and the ground truth.  | No | question, answer, ground truth (context not needed)  | in the range [0, 1]. |
| QnA Ada Similarity Evaluation | Ada Similarity  | Computes sentence (document) level embeddings using Ada embeddings API for both ground truth and prediction. Then computes cosine similarity between them (one floating point number)  | Yes | question, answer, ground truth (context not needed)  | in the range [0, 1]. |

## Ways to improve flow performance

After checking the [built-in metrics](#understand-the-built-in-evaluation-metrics) from the evaluation, you can try to improve your flow performance by:

- Check the output data to debug any potential failure of your flow.
- Modify your flow to improve its performance. This includes but not limited to:
  - Modify the prompt
  - Modify the system message
  - Modify parameters of the flow
  - Modify the flow logic

Prompt construction can be difficult. We provide a [Introduction to prompt engineering](../../cognitive-services/openai/concepts/prompt-engineering.md) to help you learn about the concept of constructing a prompt that can achieve your goal. See [prompt engineering techniques](../../cognitive-services/openai/concepts/advanced-prompt-engineering.md?pivots=programming-language-chat-completions) to learn more about how to construct a prompt that can achieve your goal.

System message, sometimes referred to as a metaprompt or [system prompt](../../cognitive-services/openai/concepts/advanced-prompt-engineering.md?pivots=programming-language-completions#meta-prompts) that can be used to guide an AI system’s behavior and improve system performance. Read this document on [System message framework and template recommendations for Large Language Models(LLMs)](../../cognitive-services/openai/concepts/system-message.md) to learn about how to improve your flow performance with system message.

## Further reading: Guidance for creating Golden Datasets used for Copilot quality assurance

The creation of a copilot that uses Large Language Models (LLMs) typically involves grounding the model in reality using source datasets. However, to ensure that the LLMs provide the most accurate and useful responses to customer queries, a "Golden Dataset" is necessary.

A Golden Dataset is a collection of realistic customer questions and expertly crafted answers. It serves as a Quality Assurance tool for LLMs used by your copilot. Golden Datasets aren't used to train an LLM or inject context into an LLM prompt. Instead, they're utilized to assess the quality of the answers generated by the LLM.

If your scenario involves a copilot or if you are in the process of building your own copilot, we recommend referring to this specific document: [Producing Golden Datasets: Guidance for creating Golden Datasets used for Copilot quality assurance](https://aka.ms/copilot-golden-dataset-guide) for more detailed guidance and best practices.

## Next steps

In this document, you learned how to submit a batch run and use a built-in evaluation method to measure the quality of your flow output. You also learned how to view the evaluation result and metrics, and how to start a new round of evaluation with a different method or subset of variants. We hope this document helps you improve your flow performance and achieve your goals with Prompt flow.

- [Develop a customized evaluation flow](how-to-develop-an-evaluation-flow.md)
- [Tune prompts using variants](how-to-tune-prompts-using-variants.md)
- [Deploy a flow](how-to-deploy-for-real-time-inference.md)
