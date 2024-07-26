---
title: Dynamic Dictionary - Translator
titleSuffix: Azure AI services
description: How to use the dynamic dictionary feature of the Azure AI Translator.
#services: cognitive-services
author: laujan
manager: nitinme
ms.service: azure-ai-translator
ms.topic: conceptual
ms.date: 12/06/2023
ms.author: lajanuar
---

# Dynamic dictionary

The dynamic dictionary feature allows you to customize translations for specific terms or phrases. You define custom translations for your unique context, language, or specific needs. If you already know the translation you want to apply to a word or a phrase, you can supply it as markup within the request. The dynamic dictionary is safe only for compound nouns like proper names and product names.

**Syntax:**

<mstrans:dictionary translation="translation of phrase">phrase</mstrans:dictionary>

**Requirements:**

* The `From` and `To` languages must include English and another supported language. 
* You must include the `From` parameter in your API translation request instead of using the autodetect feature. 

**Example: en-de:**

Source input: `The word <mstrans:dictionary translation=\"wordomatic\">wordomatic</mstrans:dictionary> is a dictionary entry.`

Target output: `Das Wort "wordomatic" ist ein Wörterbucheintrag.`

This feature works the same way with and without HTML mode.

Use the feature sparingly. A better way to customize translation is by using Custom Translator. Custom Translator makes full use of context and statistical probabilities. If you have or can create training data that shows your work or phrase in context, you get better results. You can find more information about Custom Translator at [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator).
