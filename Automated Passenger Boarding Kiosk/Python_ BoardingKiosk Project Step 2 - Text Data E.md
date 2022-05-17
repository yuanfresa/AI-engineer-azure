
### Please install the required Python modules/SDKs


```python
! activate ai-azure-c1

import sys

sys.path.append("/opt/conda/envs/ai-azure-c1/lib/python3.8/site-packages")
```

## Importing Useful Python Libraries or Packages


```python
import requests
from io import BytesIO
from PIL import Image, ImageDraw
import matplotlib.pyplot as plt
```


```python
from azure.core.credentials import AzureKeyCredential
from azure.ai.formrecognizer import FormRecognizerClient
```

# Utility Functions


```python
def show_image_in_cell(image_url):
    response = requests.get(image_url)
    img = Image.open(BytesIO(response.content))
    plt.figure(figsize=(20,10))
    plt.imshow(img)
    plt.show()
```

### Source Document


```python
id1_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-avkash-chauhan.png?raw=true"
id2_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-james-jackson.png?raw=true"
id3_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-james-webb.png?raw=true"
id4_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-libby-herold.png?raw=true"
id5_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-radha-s-kumar.png?raw=true"
id6_url = "https://github.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/blob/main/Data%20Preparation/digital_id/ca-dl-sameer-kumar.png?raw=true"
```

# Using Form Recognizer ID API to extract info


```python
AZURE_FORM_RECOGNIZER_ENDPOINT = "https://myformrecogniser195739.cognitiveservices.azure.com/"
AZURE_FORM_RECOGNIZER_KEY = "45925e9976994a6590aa36ac9c11e036"
```


```python
endpoint = AZURE_FORM_RECOGNIZER_ENDPOINT
key = AZURE_FORM_RECOGNIZER_KEY
```


```python
form_recognizer_client = FormRecognizerClient(endpoint=endpoint, credential=AzureKeyCredential(key))
```


```python
id1_content_from_url = form_recognizer_client.begin_recognize_identity_documents_from_url(id1_url)
```


```python
collected_id_cards = id1_content_from_url.result()
```


```python
type(collected_id_cards[0])
```




    azure.ai.formrecognizer._models.RecognizedForm



## Processing the results


```python
def get_id_card_details(identity_card):
    first_name = identity_card.fields.get("FirstName")
    if first_name:
        print("First Name: {} has confidence: {}".format(first_name.value, first_name.confidence))
    last_name = identity_card.fields.get("LastName")
    if last_name:
        print("Last Name: {} has confidence: {}".format(last_name.value, last_name.confidence))
    document_number = identity_card.fields.get("DocumentNumber")
    if document_number:
        print("Document Number: {} has confidence: {}".format(document_number.value, document_number.confidence))
    dob = identity_card.fields.get("DateOfBirth")
    if dob:
        print("Date of Birth: {} has confidence: {}".format(dob.value, dob.confidence))
    doe = identity_card.fields.get("DateOfExpiration")
    if doe:
        print("Date of Expiration: {} has confidence: {}".format(doe.value, doe.confidence))
    sex = identity_card.fields.get("Sex")
    if sex:
        print("Sex: {} has confidence: {}".format(sex.value, sex.confidence))
    address = identity_card.fields.get("Address")
    if address:
        print("Address: {} has confidence: {}".format(address.value, address.confidence))
    country_region = identity_card.fields.get("CountryRegion")
    if country_region:
        print("Country/Region: {} has confidence: {}".format(country_region.value, country_region.confidence))
    region = identity_card.fields.get("Region")
    if region:
        print("Region: {} has confidence: {}".format(region.value, region.confidence))
```


```python
get_id_card_details(collected_id_cards[0])
```

    First Name: AVKASH CHAUHAN has confidence: 0.76
    Last Name: CHAUHAN has confidence: 0.883
    Document Number: D1234578 has confidence: 0.995
    Date of Birth: 1990-01-01 has confidence: 0.995
    Date of Expiration: 2025-01-01 has confidence: 0.992
    Sex: X has confidence: 0.161
    Address: 1234 Circle Ave, Apt 123 San Mateo, CA, 94401 has confidence: 0.585
    Country/Region: USA has confidence: 0.99
    Region: California has confidence: 0.984



```python
for index_id, id_card in enumerate(collected_id_cards):
    print("Displaying identity card details ....... # {}".format(index_id+1))
    get_id_card_details(id_card)
    print("---------------- EOL -------------------------")
```

    Displaying identity card details ....... # 1
    First Name: AVKASH CHAUHAN has confidence: 0.76
    Last Name: CHAUHAN has confidence: 0.883
    Document Number: D1234578 has confidence: 0.995
    Date of Birth: 1990-01-01 has confidence: 0.995
    Date of Expiration: 2025-01-01 has confidence: 0.992
    Sex: X has confidence: 0.161
    Address: 1234 Circle Ave, Apt 123 San Mateo, CA, 94401 has confidence: 0.585
    Country/Region: USA has confidence: 0.99
    Region: California has confidence: 0.984
    ---------------- EOL -------------------------



```python
show_image_in_cell(id1_url)
```


![png](../../_resources/output_20_0.png)


# Custom Form Recognizer for Boarding Pass


```python
import os
from azure.core.exceptions import ResourceNotFoundError
#from azure.ai.formrecognizer import FormRecognizerClient
from azure.ai.formrecognizer import FormTrainingClient
#from azure.core.credentials import AzureKeyCredential
```


```python
form_training_client = FormTrainingClient(endpoint=endpoint, credential=AzureKeyCredential(key))
```


```python
aved_model_list = form_training_client.list_custom_models()
```


```python
# Blob SAS URL
trainingDataUrl = "https://boardingkioststorage.blob.core.windows.net/boardingkiosk?sp=racwdl&st=2022-05-17T19:45:27Z&se=2022-05-21T03:45:27Z&spr=https&sv=2020-08-04&sr=c&sig=CYzvMkofohev0vNitR1BFwbmiwEFGfhQauAcoP1OteY%3D"
```

## Labled Trainning


```python
labeled_training_process = form_training_client.begin_training(trainingDataUrl, use_training_labels=True)
labeled_custom_model = labeled_training_process.result()
```


```python
labeled_custom_model.model_id
```




    '2726c9a3-ac87-4b9c-a5c5-e2434a2b5a10'




```python
labeled_custom_model.status
```




    'ready'




```python
labeled_custom_model.training_documents
```




    [TrainingDocumentInfo(name=boarding-avkash.pdf, status=succeeded, page_count=1, errors=[], model_id=None),
     TrainingDocumentInfo(name=boarding-james-webb.pdf, status=succeeded, page_count=1, errors=[], model_id=None),
     TrainingDocumentInfo(name=boarding-james.pdf, status=succeeded, page_count=1, errors=[], model_id=None),
     TrainingDocumentInfo(name=boarding-libby.pdf, status=succeeded, page_count=1, errors=[], model_id=None),
     TrainingDocumentInfo(name=boarding-radha-s-kumar.pdf, status=succeeded, page_count=1, errors=[], model_id=None),
     TrainingDocumentInfo(name=boarding-sameer.pdf, status=succeeded, page_count=1, errors=[], model_id=None)]




```python
for doc in labeled_custom_model.training_documents:
    print("Document name: {}".format(doc.name))
    print("Document status: {}".format(doc.status))
    print("Document page count: {}".format(doc.page_count))
    print("Document errors: {}".format(doc.errors))
```

    Document name: boarding-avkash.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []
    Document name: boarding-james-webb.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []
    Document name: boarding-james.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []
    Document name: boarding-libby.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []
    Document name: boarding-radha-s-kumar.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []
    Document name: boarding-sameer.pdf
    Document status: succeeded
    Document page count: 1
    Document errors: []


### Testing


```python
new_test_url = "https://raw.githubusercontent.com/yuanfresa/Azure-AI-Engineer-Nanodegree-Project-Portfolio/main/Data%20Preparation/boarding_pass/boarding_pass_test.png"
```


```python
labeled_custom_test_action = form_recognizer_client.begin_recognize_custom_forms_from_url(model_id=labeled_custom_model.model_id, form_url=new_test_url)
```


```python
labeled_custom_test_action.status()
```




    'succeeded'




```python
labeled_custom_test_action_result = labeled_custom_test_action.result()
```


```python
for recognized_content in labeled_custom_test_action_result:
    print("Form type: {}".format(recognized_content.form_type))
    for name, field in recognized_content.fields.items():
        print("Field '{}' has label '{}' with value '{}' and a confidence score of {}".format(
            name,
            field.label_data.text if field.label_data else name,
            field.value,
            field.confidence
        ))
```

    Form type: custom:2726c9a3-ac87-4b9c-a5c5-e2434a2b5a10
    Field 'Passenger Name' has label 'Passenger Name' with value 'Zhukun Xu' and a confidence score of 0.99
    Field 'airline name' has label 'airline name' with value 'UDACITY AIRLINES' and a confidence score of 0.99
    Field 'Boarding Time' has label 'Boarding Time' with value 'None' and a confidence score of 0.906
    Field 'Baggage' has label 'Baggage' with value 'YES' and a confidence score of 0.99
    Field 'From' has label 'From' with value 'New York' and a confidence score of 0.99
    Field 'Carrier' has label 'Carrier' with value 'UA' and a confidence score of 0.99
    Field 'To' has label 'To' with value 'Stockholm' and a confidence score of 0.99
    Field 'Date' has label 'Date' with value 'May 20, 2022' and a confidence score of 0.989
    Field 'Class' has label 'Class' with value 'E' and a confidence score of 0.99
    Field 'BoardingPass ID' has label 'BoardingPass ID' with value 'ETK-737268572620C' and a confidence score of 0.99
    Field 'Gate' has label 'Gate' with value 'A10' and a confidence score of 0.99
    Field 'Seat' has label 'Seat' with value '45E' and a confidence score of 0.021
    Field 'Flight No.' has label 'Flight No.' with value '289' and a confidence score of 0.99



```python
Image.open(requests.get(new_test_url, stream=True).raw)
```




![png](../../_resources/output_38_0.png)




```python

```
