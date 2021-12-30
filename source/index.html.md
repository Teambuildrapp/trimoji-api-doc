---
title: Trimoji API Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - javascript
  - python

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Kittn API
---

# Introduction

Welcome to the Trimoji API documentation ! Our API allows you to integrate our psychometric assessment test within your own project in a very simple way.

This API is currently in Version 1, we're often shipping updates to improve performance or provide more useful endpoints.

We're not creating breakchange within the same major version, breakchanges will only occur in major update (V1 -> V2 -> V3)

Feel free to create issues on the [GitHub](https://github.com/Teambuildrapp/trimoji-api-doc) of this doc if you're facing any problem.

# Authentication

> Here is an example of request using the TOKEN:


```javascript
var axios = require('axios');
var data = JSON.stringify({
  "type": 0
});

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/init',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python
import requests
import json

url = "https://api.trimoji.fr/v1/assessment/init"

payload = json.dumps({
  "type": 0
})
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}
response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```

> Make sure to replace `YOUR_API_KEY` with your API key.

The Trimoji API uses a JWT auth token to identify you. Contact our IT department at <a href="mailto:dev@teambuildr.fr">dev@teambuildr.fr</a> to get your API key.

Your subscription to our API includes a defined amount of monthly rate. This rate is only affected by an assessment being processed (`POST /v1/assessment/save`). We're offering unlimited rate for everything else. All request must go through the `HTTPS` protocol.

Our API expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: YOUR_API_KEY`

<aside class="notice">
You must replace <code>YOUR_API_KEY</code> with your personal API key.
</aside>

# Response class


```json
{
    "success": Boolean,
    "message": String,
    "data": Object
}
```

The API response is returned in this object format.

If the request fails, `success` will be `false` and `message` will contain the error message.

If the request succeeds, `success` will be `true` and `message` will be "Success".

Datas are returned within the `data` object.


# Assessment

## Initialize an assessment test

```javascript
var axios = require('axios');
var data = JSON.stringify({
  "type": 0
});

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/init',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python
import requests
import json

url = "https://api.trimoji.fr/v1/assessment/init"

payload = json.dumps({
  "type": 0
})
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)

```

> The API returns the token of the assessment and the questions to be answered.

```json
{
    "success": true,
    "message": "Success",
    "data": {
        "token": "ec30645a-9625-4fcc-be9b-c8a296f9c540",
        "questions": [
            {
                "id": 3,
                "question": "Au théâtre, vous vous voyez plutôt sur scène que dans les coulisses",
                "question_en": "In the theater, you see yourself more on stage than behind the scenes.",
                "question_es": "En el teatro, te ves más en el escenario que detrás de escena"
            },
            {
                "id": 20,
                "question": "Les gens peuvent rarement vous contrarier.",
                "question_en": "People can rarely upset you.",
                "question_es": "La gente rara vez puede molestarte."
            },
            //...
        ]
    }
}
```

`POST /v1/assessment/init`

### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
type | Number | Correspond to the test duration. 0 for the 3 mins test, 1 for 15 mins test.

### Description

This endpoint represents the first step of the assessment. It returns the `token` of the assessment and the questions to be answered. The `token` is used to identify the assessment in the next steps and to save the answers. The questions are returned in the order they should be answered. The questions are returned as an array of objects. Each object contains the `question id` and the question in fr, en and es.

On the frontend, you need to render each question individually. The answer to theses question are in the form of a Likert scale (1-5) from disagree (1) to agree (5). The colors of each buttons needs to be the same to avoid any cognitive biases. 

[Please, click here to see a perfect example of frontend rendering](https://statics.teambuildr.fr/docs/trimoji/apidoc/likerttrimoji.png). 

When a user clicks on one of the answers (one of the five button), you need to store the answer in an array of objects. Each object contains the question id and the answer.

`userAnswers = [
  {
    "id": 3,
    "value": 1
  },
  {
    "id": 20,
    "value": 4
  },
  ...
]`

This is the array you will send to the endpoint in charge of saving the answers and processing the personnality assessment.

### Example

You request `/v1/assessment/init` which returns a UUIDV4 `token` (`ec30645a-9625-4fcc-be9b-c8a296xxxxxx`) and an array of questions.

You then display the questions in the frontend.

The user clicks on the second answer (corresponding to the value `2`) of the question with id `4`.

You then push the answer to the array
`myAnswerArrayWithWhateverName = [
  {
    "id": 4,
    "value": 2
  },
  ...
]` and so on.


## Store Test Answers

```javascript

var axios = require('axios');
var data = JSON.stringify({
    "token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
    "answers": [
        {
            "id": 3,
            "value": 1
        },
        {
            "id": 20,
            "value": 4
        },
        // ...
    ]
});

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/save',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)

```

```python
import http.client
import json

conn = http.client.HTTPSConnection("localhost", 3333)
payload = json.dumps({
    "token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
    "questions": [
        {
            "id": 3,
            "value": 1
        },
        {
            "id": 20,
            "value": 4
        },
        #...
    ]
})
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}
conn.request("POST", "/v1/assessment/save", payload, headers)
res = conn.getresponse()
data = res.read()
print(data.decode("utf-8"))

```

`POST /v1/assessment/save`

### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
token | String | The token ID of the assessment test
answers | Array | Array of object. For each answer you need to provide the `id` of the question and the `value` of the answer as integers.

### Description

This endpoint is used to store the answers of the user and to trigger the calculation of the personality. This needs to be done only once, we recommend creating a new assessment each time someone wants to take the test, or if you want a user to take the test multiple times.

<aside class="warning">You can store more answers if you want, but you need to make sure that the answers are not already stored, and we can't guarantee that the personality will be correct if you do so. That's why we don't provide an endpoint to only load questions and we're forcing you to get the questions by creating a new assessment.</aside>


## Store Soft Skills Feedbacks

```javascript
var axios = require('axios');
var data = JSON.stringify({
        "token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
        "value": 4,
        "isGlobal": 1,
        "quality_id": 4
    });

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/feedback/softskills',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python

import requests
import json

url = "https://api.trimoji.fr/v1/assessment/feedback/softskills"

payload=json.dumps({
      "token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
      "value": 4,
      "isGlobal": 1,
      "quality_id": 4
    })
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)


```

### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
token | String | The token of the profile who give the feedbacks
value | Integer | Answer between 1 and 5. 
isGlobal | Integer | 1 if the feebacks concerns all qualities of a test, 0 if it concerns just one
quality_id | Integer | `id` of the quality if the feedbacks is for one quality

## Store Trimoji Feedbacks 

```javascript
var axios = require('axios');
var data = JSON.stringify({
      "token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
      "global_value": 4,
      "structure_value": 3,
      "motivation_value": 2,
      "intensity_value": 1
  });

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/feedback/trimoji',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python

import requests
import json

url = "https://api.trimoji.fr/v1/assessment/feedback/trimoji"

payload=json.dumps({"token":"87a2ca5d-3025-43d6-9d05-b2831b2d1256","global_value":4,"structure_value":3,"motivation_value":2,"intensity_value":1})
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)

```
### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
token | String | The token of the profile who give the feedbacks
global_value | Integer | Feedback answer for the all trimoji between 1 and 5. 
structure_value | Integer | Feedback answer for the first emoji between 1 and 5.  
motivation_value | Integer | Feedback answer for the second emoji between 1 and 5. 
intensity_value | Integer | Feedback answer for the third emoji between 1 and 5. 

## Get Profile

```javascript
var axios = require('axios');
var data = JSON.stringify(
    {"token":"87a2ca5d-3025-43d6-9d05-b2831b2d1256"}
  );

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/assessment/get',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python
import requests
import json

url = "https://api.trimoji.fr/v1/assessment/get"

payload=json.dumps(
  {"token":"87a2ca5d-3025-43d6-9d05-b2831b2d1256"}
)
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)
```

> The API returns the full profile for a given token.

```json
"data": {
        "trimoji": {
            "envStructure": "🃏",
            "envMotivation": "🤝",
            "envIntensity": "⏰",
            "trimoji": "🃏🤝⏰",
            "trimojiFeedback": [
                {
                    "id": "4d2fbeb5-62c5-45c0-97e7-2c3f4f1aeed8",
                    "test_token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
                    "global_value": 4,
                    "structure_value": 3,
                    "motivation_value": 2,
                    "intensity_value": 1,
                }
            ]
        },
        "softskills": {
            "softskills": [
                {
                    "id": 44,
                    "name": "Déterminé(e) et passionné(e)",
                    "name_en": "Determined and passionate",
                    "name_es": "Decidido y apasionado",
                    "isQuality": true
                },
                // ...
            ],
            "softskillsFeedback": [
                {
                    "id": "5fa1acf6-f067-4d97-bce2-4f3a28fd2c52",
                    "test_token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
                    "quality_id": null,
                    "value": 4,
                    "isGlobal": true,
                },
                // ...
            ]
        },
        "personality": {
            "name": "Créateur ouvert d’esprit",
            "name_en": "Open Minded Creative",
            "mantra": "Capacité à évaluer et à organiser des concepts. Les responsabilités reviennent aux meilleurs cerveaux",
            "mantra_en": "Ability to evaluate and organize concepts. Responsibilities go to the best brains.",
            "descriptions": {
                // too long
            }
        },
        "environments": [
            {
                "name": "Travailler seul ou en petits groupes avec des gestionnaires simples et réalistes",
                "name_en": "Self directed work or in small groups with simple and realistic managers",
                "name_es": "Trabaje solo o en grupos pequeños con gerentes simples y realistas",
                "isBeneficial": true
            },
            {
                "name": "Travail en équipe seulement",
                "name_en": "Teamwork only",
                "name_es": "Solo trabajo en equipo",
                "isBeneficial": false
            }
        ],
        "positions": [
            {
                "id": 56,
                "name": "Chefs de projet",
                "name_en": "Project managers",
                "name_es": "Jefes de proyecto"
            },
            // ...
        ]
    }
```

`GET /v1/assessment/get`

### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
token | String | The token of the profile who you want the informations to be returns.

### Description

This endpoint returns the full personnality profile of a given assessment token. This profile is the Trimoji psychometric test result, this is basicly the personnality of the user.

<aside class="warning">The profile is only available once you have saved the assessment answers of the user. (see the <code>Store Test Answers</code> section for more information)</aside>

This profile contains the Trimoji, the softskills, the main personality traits, the best and worst working environments, the most common job position for this personnality, the descriptions as manager, colleague, etc. 

The API also returns the feedbacks of the user for the Trimoji and softskills if there is one or more. The feedbacks are given by the user, you can refer to the [softskills feedback section](#store-soft-skills-feedbacks) and [trimoji feedback section](#store-trimoji-feedbacks) for more informations.

Check the [Trimoji website](https://trimoji.fr/) for more informations about the Trimoji profile.

NOTE : We recommend storing the API response in your database to avoid the need to call the API every time you need the profile of your user.


# Other routes

## Get all company tests

```javascript
var axios = require('axios');
var data = '';

var config = {
  method: 'get',
  url: 'https://api.trimoji.fr/v1/assessment/list',
  headers: { 
    'Authorization': 'YOUR_API_KEY'
  },
  data : data
};

axios(config)
```

```python
import requests

url = "https://api.trimoji.fr/v1/assessment/list"

payload={}
headers = {
  'Authorization': 'YOUR_API_KEY'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)

```

> The above command returns JSON structured like this:

```json
 "data": [
        {
          "id": "c9e93efd-fbe1-4c2c-9ee4-aec490f3bcd4",
          "testId": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
          "updated_at": "2021-12-30T14:09:25.000Z",
          "type": 0,
          "personality": {
              "name": "Créateur ouvert d’esprit",
              "name_en": "Open Minded Creative"
          },
          "trimoji": {
              "envStructure": "🃏",
              "envMotivation": "🤝",
              "envIntensity": "⏰",
              "trimoji": "🃏🤝⏰",
              "trimojiFeedback": {
                  "id": "4d2fbeb5-62c5-45c0-97e7-2c3f4f1aeed8",
                  "test_token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
                  "global_value": 4,
                  "structure_value": 3,
                  "motivation_value": 2,
                  "intensity_value": 1,
                  "created_at": "2021-12-30T14:13:52.000Z",
              }
          },
          "softskillsFeedback": [
              {
                  "id": "5fa1acf6-f067-4d97-bce2-4f3a28fd2c52",
                  "test_token": "87a2ca5d-3025-43d6-9d05-b2831b2d1256",
                  "quality_id": null,
                  "value": 4,
                  "isGlobal": true,
                  "created_at": "2021-12-30T14:13:46.000Z",
              }
          ]
        },
        {
            "id": "8183a739-24db-46c6-b3dd-7ded17e13d83",
            "testId": "945343b5-401e-4ee4-b8b7-e08b34ac5481",
            "updated_at": "2021-12-30T13:49:08.000Z",
            "type": 0,
            "personality": {
                "name": "Créateur ouvert d’esprit",
                "name_en": "Open Minded Creative"
            },
            "trimoji": {
                "envStructure": "🃏",
                "envMotivation": "🤝",
                "envIntensity": "⏰",
                "trimoji": "🃏🤝⏰",
                "trimojiFeedback": null
            },
            "softskillsFeedback": []
        }
    ]
```

This endpoint returns an Array of the results of all tests for a company. It returns for each test, its token, type, personality and trimoji as well as the the feedbacks for the quality and the trimoji. 

## Get relations 

```javascript
var axios = require('axios');
var data = JSON.stringify({"token":"01d35a66-1a01-4aae-9464-d5f9870c7a18","team":["01b25826-f9f5-4153-be19-64bfefc34697","01376043-ec63-4128-8cfe-2afe6ab37ece"]});

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/relations',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python
import requests

url = "https://api.trimoji.fr/v1/relations"

payload="{'token':'01d35a66-1a01-4aae-9464-d5f9870c7a18','team':['01b25826-f9f5-4153-be19-64bfefc34697','01376043-ec63-4128-8cfe-2afe6ab37ece']}"
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)

```

> The above command returns JSON structured like this:

```json
 "data": [
        {
            "token": "01376043-ec63-4128-8cfe-2afe6ab37ece",
            "relationCaracteristics": [
                {
                    "name": "Etre eux-mêmes l'un avec l'autre sans provoquer d'incompréhensions",
                    "name_en": "Be themselves with each other without causing misunderstandings",
                    "name_es": null,
                    "is_positive": true
                },
                {
                    "name": "Compréhension intuitive et correcte de l'autre et sont rarement surpris par ce que celui-ci dit ou fait",
                    "name_en": "Intuitive and correct understanding of others and are rarely surprised by what they say or do",
                    "name_es": null,
                    "is_positive": true
                },
                {
                    "name": "Arrivent aisément à un consensus",
                    "name_en": "Easily reach consensus",
                    "name_es": null,
                    "is_positive": true
                },
                {
                    "name": "Cette relation est fortement orientée sur la communication verbale",
                    "name_en": "This relationship is strongly oriented on verbal communication",
                    "name_es": null,
                    "is_positive": true
                },
                {
                    "name": "Potentielles discussions incessantes",
                    "name_en": "Potential incessant discussions",
                    "name_es": null,
                    "is_positive": false
                },
                {
                    "name": "Volonté de se séparer pour travailler et trouver du repos",
                    "name_en": "Willingness to separate in order to work and find rest",
                    "name_es": null,
                    "is_positive": false
                }
            ],
            "type": "Miroir",
            "type_en": "Miroir",
            "score": 70,
            "description": "Ces partenaires peuvent être eux-mêmes l'un avec l'autre sans provoquer d'incompréhensions. Ils ont une compréhension intuitive et correcte de l'autre et sont rarement surpris par ce que celui-ci dit ou fait. C'est pour cette raison qu'ils n'argumentent que très rarement. Ils ont toujours des choses à dire sur un même sujet et arrivent aisément à un consensus tout en mettant l'accent sur certaines choses, ce qui crée un effet révisionniste.\n\n Cette relation est fortement orientée sur la communication verbale, avec des partenaires discutant de sujets relatifs à leurs hobbys (et évitant les autres), révisant et ajustant ensemble leurs points de vue. Ces partenaires peuvent avoir tendance à se fatiguer de cet aspect discussions incessantes qui est la nature même de la relation, et peuvent vouloir se séparer pour travailler et trouver du repos. Ces partenaires s'animent vivement dès lors que quelqu'un d'autre leur demande lequel est le double de l'autre et lequel des deux stimule l'autre.",
            "description_en": "These partners can be themselves with each other without causing misunderstandings. They have an intuitive and correct understanding of the other and are seldom surprised by what this one says or does. It is for this reason that they very rarely argue. They always have things to say on the same subject and easily come to a consensus while emphasizing certain things, which creates a revisionist effect.  N  n This relationship is strongly oriented towards verbal communication, with partners discussing topics related to their hobbies (and avoiding others), reviewing and adjusting their views together. These partners may tend to get tired of this endless arguing aspect that is the very nature of the relationship, and may want to go their separate ways in order to work and find rest. These partners get excited when someone else asks them which is the double of the other and which of the two stimulates the other."
        },
        {
            "token": "01b25826-f9f5-4153-be19-64bfefc34697",
            "relationCaracteristics": [
                {
                    "name": "Un(e) partenaire (le récepteur) trouve qu'il est en permanence en train d'essayer de résoudre les problèmes de l'autre",
                    "name_en": "One partner (the receiver) finds that he or she is constantly trying to solve the other's problems",
                    "name_es": null,
                    "is_positive": false
                },
                {
                    "name": "Trop impliqué(e) émotionnellement dans la vie de l'autre partenaire",
                    "name_en": "Too emotionally involved in the life of the other partner",
                    "name_es": null,
                    "is_positive": false
                },
                {
                    "name": "Toujours en attente d'une récompense de la part de l'émetteur",
                    "name_en": "Still waiting for a reward from the issuer",
                    "name_es": null,
                    "is_positive": false
                }
            ],
            "type": "Requête Recepteur ",
            "type_en": "Receiver Request ",
            "score": 22,
            "description": "Relation asymétrique de type requête en mode émetteur/récepteur. Un partenaire (le récepteur) trouve qu'il est en permanence en train d'essayer de résoudre les problèmes de l'autre (l'émetteur) mais est trop impliqué émotionnellement dans la vie de l'autre partenaire - toujours en attente d'une récompense de la part de l'émetteur.\n\n L'émetteur, de son côté, ignore largement tout cela et se demande pourquoi le récepteur est si dépendant et si sensible aux choses qu'il lui dit.",
            "description_en": "Asymmetric query type relationship in sender / receiver mode. One partner (the receiver) finds that he is constantly trying to solve the problems of the other (the sender) but is too emotionally involved in the life of the other partner - still waiting for a reward from the sender. \n \n The sender, on the other hand, largely ignores all of this and wonders why the receiver is so dependent and so sensitive to things that 'he tells him."
        }
    ]
```

This endpoint returns an Array of all the relations between a profile and a list of profiles. 


### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
token | String | The token of the profile to be compared with the team.
team | Array<String> | The tokens of each members of the teams.

## Get conflics 

```javascript
var axios = require('axios');
var data = JSON.stringify({"team":["01d35a66-1a01-4aae-9464-d5f9870c7a18","01b25826-f9f5-4153-be19-64bfefc34697","01376043-ec63-4128-8cfe-2afe6ab37ece"]});

var config = {
  method: 'post',
  url: 'https://api.trimoji.fr/v1/conflicts',
  headers: { 
    'Authorization': 'YOUR_API_KEY', 
    'Content-Type': 'application/json'
  },
  data : data
};

axios(config)
```

```python
import requests
import json

url = "https://api.trimoji.fr/v1/conflicts"

payload=json.dumps({"team":["01d35a66-1a01-4aae-9464-d5f9870c7a18","01b25826-f9f5-4153-be19-64bfefc34697","01376043-ec63-4128-8cfe-2afe6ab37ece"]})
headers = {
  'Authorization': 'YOUR_API_KEY',
  'Content-Type': 'application/json'
}

response = requests.request("POST", url, headers=headers, data=payload)

print(response.text)

```

> The API returns the potential conflicts in a team. 

```json
 "data": [
        {
            "token": "01376043-ec63-4128-8cfe-2afe6ab37ece",
            "conflict": false,
            "conflictCount": 0
        },
        {
            "token": "01b25826-f9f5-4153-be19-64bfefc34697",
            "conflict": true,
            "conflictCount": 1
        },
        {
            "token": "01d35a66-1a01-4aae-9464-d5f9870c7a18",
            "conflict": true,
            "conflictCount": 1
        }
    ]
```


### Route parameters

Parameter | Type | Description
--------- | ------- | -----------
team | Array | Array of the tokens of the member of the team you want to detect the potential conflicts in.


## Get All Soft Skills

```javascript
var axios = require('axios');
var data = '';

var config = {
  method: 'get',
  url: 'https://api.trimoji.fr/v1/tbr/list/qualities',
  headers: { 
    'Authorization': 'YOUR_API_KEY'
  },
  data : data
};

axios(config)
```

```python
import requests

url = "https://api.trimoji.fr/v1/tbr/list/qualities"

payload={}
headers = {
  'Authorization': 'YOUR_API_KEY'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)

```

> The API returns all the soft skills available in our database.

```json
 "data": {
        "qualities": [
            {
                "id": 1,
                "name": "Propension à la tolérance",
                "name_en": "Tolerant",
                "name_es": "Propensión a la tolerancia",
                "isQuality": true
            },
            {
                "id": 2,
                "name": "Fiable",
                "name_en": "Reliable",
                "name_es": "De confianza",
                "isQuality": true
            },
            {
                "id": 82,
                "name": "Rationnel(le)",
                "name_en": "Rational",
                "name_es": "Racional",
                "isQuality": true
            },
            {
                "id": 83,
                "name": "Spontané(e)",
                "name_en": "Spontaneous",
                "name_es": "Espontáneo",
                "isQuality": true
            }
            // ...
        ]
    }
```

## Get All Ideal Jobs

```javascript
var axios = require('axios');
var data = '';

var config = {
  method: 'get',
  url: 'https://api.trimoji.fr/v1/tbr/list/idealjob',
  headers: { 
    'Authorization': 'YOUR_API_KEY'
  },
  data : data
};

axios(config)
```

```python
import requests

url = "https://api.trimoji.fr/v1/tbr/list/idealjob"

payload={}
headers = {
  'Authorization': 'YOUR_API_KEY'
}

response = requests.request("GET", url, headers=headers, data=payload)

print(response.text)

```

> The API returns all the ideal jobs available in our database.

```json
  "data": {
        "positions": [
            {
                "id": 15,
                "name": "Acteur",
                "name_en": "Actor",
                "name_es": "Actor"
            },
            {
                "id": 24,
                "name": "Acteur",
                "name_en": "Actor",
                "name_es": "Actor"
            },
            // ...
        ]
    }
```

## END



`GET http://example.com/api/kittens`

### Query Parameters

Parameter | Default | Description
--------- | ------- | -----------
include_cats | false | If set to true, the result will also include cats.
available | true | If set to false, the result will include kittens that have already been adopted.

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.get(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.get(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "name": "Max",
  "breed": "unknown",
  "fluffiness": 5,
  "cuteness": 10
}
```

This endpoint retrieves a specific kitten.

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to retrieve

## Delete a Specific Kitten

```ruby
require 'kittn'

api = Kittn::APIClient.authorize!('meowmeowmeow')
api.kittens.delete(2)
```

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.delete(2)
```

```shell
curl "http://example.com/api/kittens/2" \
  -X DELETE \
  -H "Authorization: meowmeowmeow"
```

```javascript
const kittn = require('kittn');

let api = kittn.authorize('meowmeowmeow');
let max = api.kittens.delete(2);
```

> The above command returns JSON structured like this:

```json
{
  "id": 2,
  "deleted" : ":("
}
```

This endpoint deletes a specific kitten.

### HTTP Request

`DELETE http://example.com/kittens/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the kitten to delete

