---
author: laujan
ms.service: azure-ai-document-intelligence
ms.custom:
  - ignite-2023
ms.topic: include
ms.date: 11/15/2023
ms.author: lajanuar
---
<!-- markdownlint-disable MD041 -->

* For best results, provide one clear photo or high-quality scan per document.

* Supported file formats:

    |Model | PDF |Image: </br>JPEG/JPG, PNG, BMP, TIFF, HEIF | Microsoft Office: </br> Word (DOCX), Excel (XLSX), PowerPoint (PPTX), and HTML|
    |--------|:----:|:-----:|:---------------:
    |Read            | ✔    | ✔    | ✔  |
    |Layout          | ✔  | ✔ | ✔ (2024-02-29-preview, 2023-10-31-preview)  |
    |General&nbsp;Document| ✔  | ✔ |   |
    |Prebuilt        |  ✔  | ✔ |   |
    |Custom extraction |  ✔  | ✔ |   |
    |Custom classification  |  ✔  | ✔ | ✔ (2024-02-29-preview)  |

* For PDF and TIFF, up to 2000 pages can be processed (with a free tier subscription, only the first two pages are processed).

* The file size for analyzing documents is 500 MB for paid (S0) tier and 4 MB for free (F0) tier.

* Image dimensions must be between 50 x 50 pixels and 10,000 px x 10,000 pixels.

* If your PDFs are password-locked, you must remove the lock before submission.

* The minimum height of the text to be extracted is 12 pixels for a 1024 x 768 pixel image. This dimension corresponds to about `8`-point text at 150 dots per inch (DPI).

* For custom model training, the maximum number of pages for training data is 500 for the custom template model and 50,000 for the custom neural model.

  * For custom extraction model training, the total size of training data is 50 MB for template model and 1G-MB for the neural model.

  * For custom classification model training, the total size of training data is `1GB`  with a maximum of 10,000 pages.
