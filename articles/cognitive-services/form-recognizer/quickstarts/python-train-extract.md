---
title: 'Démarrage rapide : Entraîner un modèle et extraire des données de formulaire à l’aide d’une API REST avec Python - Form Recognizer'
titleSuffix: Azure Cognitive Services
description: Dans ce guide de démarrage rapide, vous utilisez l’API REST Form Recognizer avec Python pour entraîner un modèle et extraire des données à partir de formulaires.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: pafarley
ms.openlocfilehash: b9985bfa15cf300f82a0d24400ed1167a2d3f135
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537578"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-python"></a>Démarrage rapide : Entraîner un modèle Form Recognizer et extraire des données à partir de formulaires au moyen d’une API REST avec Python

Dans ce guide de démarrage rapide, vous utilisez l’API REST Azure Form Recognizer avec Python pour entraîner et scorer des formulaires afin d’extraire des paires clé-valeur et des tables.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Pour suivre cette procédure de démarrage rapide, vous avez besoin des éléments suivants :
- Accès à la préversion à accès limité de Form Recognizer. Pour accéder à la préversion, remplissez et envoyez le formulaire de [demande d’accès Form Recognizer](https://aka.ms/FormRecognizerRequestAccess).
- [Python](https://www.python.org/downloads/) doit être installé (si vous souhaitez exécuter l’exemple en local).
- Au minimum un ensemble de cinq formulaires du même type. Vous allez utiliser ces données pour entraîner le modèle. Vous pouvez utiliser un [exemple de jeu de données](https://go.microsoft.com/fwlink/?linkid=2090451) pour ce guide de démarrage rapide. Chargez les données vers la racine d’un compte de stockage d’objets Blob Azure.

## <a name="create-a-form-recognizer-resource"></a>Créer une ressource Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Entraîner un modèle Form Recognizer

Avant toute chose, vous avez besoin d’un jeu de données d’entraînement dans un conteneur Azure Storage Blob. Vous devez disposer au minimum de cinq formulaires remplis (documents PDF et/ou images) d’un type ou d’une structure identique en tant que données d’entrée principales. Vous pouvez également utiliser un formulaire vide avec deux formulaires remplis. Le nom de fichier du formulaire vide doit inclure le mot « vide ». Consultez [Créer un jeu de données d’apprentissage pour un modèle personnalisé](../build-training-data-set.md) pour obtenir des conseils et des options pour constituer vos données d’apprentissage.

Pour entraîner un modèle Form Recognizer à l’aide des documents de votre conteneur d’objets blob Azure, appelez l’API **Train (Entraîner)** en exécutant le code Python ci-dessous. Avant d’exécuter le code, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par l’URL du point de terminaison de la ressource Form Recognizer de la région Azure dans laquelle vous avez obtenu vos clés d’abonnement.
1. Remplacez `<Subscription key>` par la clé d’abonnement que vous avez copiée à l’étape précédente.
1. Remplacez `<SAS URL>` par l’URL de signature d’accès partagé (SAS) du conteur de stockage Azure Blob. Pour la retrouver, ouvrez l’Explorateur Stockage Microsoft Azure, cliquez avec le bouton droit sur votre conteneur et sélectionnez **Obtenir la signature d’accès partagé**. Assurez-vous que les autorisations de **Lecture** et **Écriture** sont cochées, puis cliquez sur **Créer**. Copiez alors la valeur dans la section **URL**. Il doit avoir le format : `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

    ```python
    ########### Python Form Recognizer Train #############
    from requests import post as http_post

    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    source = r"<SAS URL>"
    headers = {
        # Request headers
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key': '<Subscription Key>',
    }
    url = base_url + "/train" 
    body = {"source": source}
    try:
        resp = http_post(url = url, json = body, headers = headers)
        print("Response status code: %d" % resp.status_code)
        print("Response body: %s" % resp.json())
    except Exception as e:
        print(str(e))
    ```
1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognize-train.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognize-train.py`.

Vous recevez une réponse `200 (Success)` avec la sortie JSON suivante :

```json
{
  "modelId": "59e2185e-ab80-4640-aebc-f3653442617b",
  "trainingDocuments": [
    {
      "documentName": "Invoice_1.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_2.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_3.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_4.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    },
    {
      "documentName": "Invoice_5.pdf",
      "pages": 1,
      "errors": [],
      "status": "success"
    }
  ],
  "errors": []
}
```

Notez la valeur `"modelId"`. Vous en aurez besoin dans les étapes suivantes.
  
## <a name="extract-key-value-pairs-and-tables-from-forms"></a>Extraire des paires clé-valeur et des tables à partir de formulaires

À présent, vous allez analyser un document et en extraire des tables et des paires clé-valeur. Appelez l’API **Model - Analyze (Modèle - Analyser)** en exécutant le script Python ci-dessous. Avant d’exécuter la commande, apportez les modifications suivantes :

1. Remplacez `<Endpoint>` par le point de terminaison que vous avez obtenu avec votre clé d’abonnement Form Recognizer. Vous la trouverez sous l’onglet **Vue d’ensemble** de la ressource Form Recognizer.
1. Remplacez `<path to your form>` par le chemin d’accès du fichier de votre formulaire (par exemple, C:\temp\file.pdf).
1. Remplacez `<modelID>` par l’ID de modèle que vous avez reçu à la section précédente.
1. Remplacez `<file type>` par le type de fichier. Types pris en charge : `application/pdf`, `image/jpeg`, `image/png`.
1. Remplacez `<subscription key>` par votre clé d’abonnement.

    ```python
    ########### Python Form Recognizer Analyze #############
    from requests import post as http_post
    
    # Endpoint URL
    base_url = r"<Endpoint>" + "/formrecognizer/v1.0-preview/custom"
    file_path = r"<path to your form>"
    model_id = "<modelID>"
    headers = {
        # Request headers
        'Content-Type': '<file type>',
        'Ocp-Apim-Subscription-Key': '<subscription key>',
    }

    try:
        url = base_url + "/models/" + model_id + "/analyze" 
        with open(file_path, "rb") as f:
            data_bytes = f.read()  
        resp = http_post(url = url, data = data_bytes, headers = headers)
        print("Response status code: %d" % resp.status_code)    
        print("Response body:\n%s" % resp.json())   
    except Exception as e:
        print(str(e))
    ```

1. Enregistrez le code dans un fichier avec une extension .py. Par exemple, *form-recognize-analyze.py*.
1. Ouvrir une fenêtre d’invite de commandes.
1. À l’invite, utilisez la commande `python` pour exécuter l’exemple. Par exemple : `python form-recognize-analyze.py`.

### <a name="examine-the-response"></a>Examiner la réponse

Une réponse correcte est retournée au format JSON. Elle représente les paires clé-valeur et les tables extraites à partir du formulaire :

```bash
{
  "status": "success",
  "pages": [
    {
      "number": 1,
      "height": 792,
      "width": 612,
      "clusterId": 0,
      "keyValuePairs": [
        {
          "key": [
            {
              "text": "Address:",
              "boundingBox": [
                57.4,
                683.1,
                100.5,
                683.1,
                100.5,
                673.7,
                57.4,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "1 Redmond way Suite",
              "boundingBox": [
                57.4,
                671.3,
                154.8,
                671.3,
                154.8,
                659.2,
                57.4,
                659.2
              ],
              "confidence": 0.86
            },
            {
              "text": "6000 Redmond, WA",
              "boundingBox": [
                57.4,
                657.1,
                146.9,
                657.1,
                146.9,
                645.5,
                57.4,
                645.5
              ],
              "confidence": 0.86
            },
            {
              "text": "99243",
              "boundingBox": [
                57.4,
                643.4,
                85,
                643.4,
                85,
                632.3,
                57.4,
                632.3
              ],
              "confidence": 0.86
            }
          ]
        },
        {
          "key": [
            {
              "text": "Invoice For:",
              "boundingBox": [
                316.1,
                683.1,
                368.2,
                683.1,
                368.2,
                673.7,
                316.1,
                673.7
              ]
            }
          ],
          "value": [
            {
              "text": "Microsoft",
              "boundingBox": [
                374,
                687.9,
                418.8,
                687.9,
                418.8,
                673.7,
                374,
                673.7
              ],
              "confidence": 1
            },
            {
              "text": "1020 Enterprise Way",
              "boundingBox": [
                373.9,
                673.5,
                471.3,
                673.5,
                471.3,
                659.2,
                373.9,
                659.2
              ],
              "confidence": 1
            },
            {
              "text": "Sunnayvale, CA 87659",
              "boundingBox": [
                373.8,
                659,
                479.4,
                659,
                479.4,
                645.5,
                373.8,
                645.5
              ],
              "confidence": 1
            }
          ]
        }
      ],
      "tables": [
        {
          "id": "table_0",
          "columns": [
            {
              "header": [
                {
                  "text": "Invoice Number",
                  "boundingBox": [
                    38.5,
                    585.2,
                    113.4,
                    585.2,
                    113.4,
                    575.8,
                    38.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "34278587",
                    "boundingBox": [
                      38.5,
                      547.3,
                      82.8,
                      547.3,
                      82.8,
                      537,
                      38.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Date",
                  "boundingBox": [
                    139.7,
                    585.2,
                    198.5,
                    585.2,
                    198.5,
                    575.8,
                    139.7,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/18/2017",
                    "boundingBox": [
                      139.7,
                      546.8,
                      184,
                      546.8,
                      184,
                      537,
                      139.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Invoice Due Date",
                  "boundingBox": [
                    240.5,
                    585.2,
                    321,
                    585.2,
                    321,
                    575.8,
                    240.5,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "6/24/2017",
                    "boundingBox": [
                      240.5,
                      546.8,
                      284.8,
                      546.8,
                      284.8,
                      537,
                      240.5,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "Charges",
                  "boundingBox": [
                    341.3,
                    585.2,
                    381.2,
                    585.2,
                    381.2,
                    575.8,
                    341.3,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "$56,651.49",
                    "boundingBox": [
                      387.6,
                      546.4,
                      437.5,
                      546.4,
                      437.5,
                      537,
                      387.6,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            },
            {
              "header": [
                {
                  "text": "VAT ID",
                  "boundingBox": [
                    442.1,
                    590,
                    474.8,
                    590,
                    474.8,
                    575.8,
                    442.1,
                    575.8
                  ]
                }
              ],
              "entries": [
                [
                  {
                    "text": "PT",
                    "boundingBox": [
                      447.7,
                      550.6,
                      460.4,
                      550.6,
                      460.4,
                      537,
                      447.7,
                      537
                    ],
                    "confidence": 1
                  }
                ]
              ]
            }
          ]
        }
      ]
    }
  ],
  "errors": []
}
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez utilisé les API REST Form Recognizer avec Python pour entraîner un modèle et l’exécuter dans un exemple de scénario. Consultez à présent la documentation de référence pour explorer l’API Form Recognizer plus en détail.

> [!div class="nextstepaction"]
> [Documentation de référence sur l’API REST](https://aka.ms/form-recognizer/api)
