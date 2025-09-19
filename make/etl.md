# Procesos ETL en Make  

Los procesos de **Extracción, Transformación y Carga (ETL)** se implementaron en **Make** para automatizar la captura de datos provenientes de correos generados por systeme.io, su limpieza/normalización y la posterior carga en **Google Sheets** como fuente centralizada.  

Durante esta etapa se detectaron errores de colecta (campos mal estructurados, datos incompletos o formatos inconsistentes). Para resolverlos, se diseñaron **escenarios base reutilizables** que luego se replicaron y adaptaron en múltiples flujos ETL.

---

## Escenario base 1: Corrección de datos con IA  

Flujo utilizado como plantilla para varios procesos donde era necesario **corregir errores de carga** y **sanear JSON** de forma confiable.  
- **Fuente:** correos entrantes en Gmail con datos en crudo.  
- **Transformación:** uso de IA para **extraer el bloque entre llaves** y devolver un **JSON válido** (pares clave-valor, sin comas sobrantes), listo para parsear.  
- **Carga:** registro final en Google Sheets con información normalizada.  

### Imagen del canvas  
![Escenario corrección con IA](./img/escenario%20correccion%20con%20gemini.jpeg)

### Blueprint del escenario (JSON)  
<details>
<summary>Ver código JSON</summary>

```json
{
    "name": "Uplin Community (versión para corrección de error de carga)",
    "flow": [
        {
            "id": 1,
            "module": "google-email:TriggerNewEmail",
            "version": 2,
            "parameters": {
                "folder": "INBOX",
                "xGmRaw": "from:contacto@uplinhr.com subject:\"INSCRIPCION A UPLIN COMMUNITY\"",
                "account": 4854887,
                "markSeen": true,
                "maxResults": 100,
                "searchType": "gmail"
            },
            "mapper": {},
            "metadata": {
                "designer": {
                    "x": 130,
                    "y": 20
                },
                "restore": {
                    "parameters": {
                        "folder": {
                            "path": [
                                "INBOX"
                            ]
                        },
                        "account": {
                            "data": {
                                "scoped": "true",
                                "connection": "google-restricted"
                            },
                            "label": "My Google Restricted connection (uplinrh.master@gmail.com)"
                        },
                        "searchType": {
                            "label": "Gmail filter"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "account",
                        "type": "account:google-restricted",
                        "label": "Connection",
                        "required": true
                    },
                    {
                        "name": "searchType",
                        "type": "select",
                        "label": "Filter type",
                        "required": true,
                        "validate": {
                            "enum": [
                                "simple",
                                "gmail"
                            ]
                        }
                    },
                    {
                        "name": "markSeen",
                        "type": "boolean",
                        "label": "Mark email message(s) as read when fetched"
                    },
                    {
                        "name": "maxResults",
                        "type": "uinteger",
                        "label": "Maximum number of results",
                        "required": true
                    },
                    {
                        "name": "folder",
                        "type": "folder",
                        "label": "Folder",
                        "required": true
                    },
                    {
                        "name": "xGmRaw",
                        "type": "text",
                        "label": "Query",
                        "required": true
                    }
                ]
            }
        },
        {
            "id": 14,
            "module": "gemini-ai:createACompletionGeminiPro",
            "version": 1,
            "parameters": {
                "__IMTCONN__": 4441182
            },
            "mapper": {
                "model": "gemini-1.5-flash",
                "contents": [
                    {
                        "role": "model",
                        "parts": [
                            {
                                "text": "Recibirás un texto en {{1.text}} que contiene un bloque entre llaves {}.\r\n\r\n1. Extrae únicamente el contenido completo entre la primera llave de apertura { y la llave de cierre } correspondiente.\r\n2. Asegúrate de que el resultado sea un objeto JSON válido:\r\n   - Cada par clave-valor debe estar entre comillas dobles.\r\n   - Separa los pares con comas.\r\n   - El último par clave-valor NO debe tener coma antes de la llave de cierre.\r\n3. Devuelve el resultado como texto plano, sin ningún carácter adicional antes o después.\r\n\r\nEjemplo:\r\nEntrada en {{1.text}}:\r\nLorem ipsum {\r\n  \"Correo\" : \"ejemplo@gmail.com\",\r\n  \"Nombre\" : \"Juan\",\r\n  \"Apellido\" : \"Pérez\",\r\n  \"Teléfono\" : \"541100000000\",\r\n}\r\nGracias.\r\n\r\nSalida esperada:\r\n{\r\n  \"Correo\" : \"ejemplo@gmail.com\",\r\n  \"Nombre\" : \"Juan\",\r\n  \"Apellido\" : \"Pérez\",\r\n  \"Teléfono\" : \"541100000000\"\r\n}\r\n",
                                "type": "text"
                            }
                        ]
                    },
                    {
                        "role": "user",
                        "parts": [
                            {
                                "text": "Toma el texto recibido en {{1.text}}. \r\nBusca el bloque que está entre llaves { } y devuélvelo como un objeto JSON válido. \r\nEl último par clave-valor no debe llevar coma antes de la llave de cierre. \r\nNo agregues explicaciones, comentarios ni texto adicional: la salida debe ser únicamente el bloque JSON limpio.\r\n",
                                "type": "text"
                            }
                        ]
                    }
                ],
                "generationConfig": {
                    "thinkingConfig": {}
                },
                "system_instruction": {}
            },
            "metadata": {
                "designer": {
                    "x": 481,
                    "y": 10
                },
                "restore": {
                    "expect": {
                        "model": {
                            "mode": "chose",
                            "label": "Gemini 1.5 Flash"
                        },
                        "contents": {
                            "mode": "chose",
                            "items": [
                                {
                                    "role": {
                                        "mode": "chose",
                                        "label": "Model"
                                    },
                                    "parts": {
                                        "mode": "chose",
                                        "items": [
                                            {
                                                "type": {
                                                    "mode": "chose",
                                                    "label": "Text"
                                                }
                                            }
                                        ]
                                    }
                                },
                                {
                                    "role": {
                                        "mode": "chose",
                                        "label": "User"
                                    },
                                    "parts": {
                                        "mode": "chose",
                                        "items": [
                                            {
                                                "type": {
                                                    "mode": "chose",
                                                    "label": "Text"
                                                }
                                            }
                                        ]
                                    }
                                }
                            ]
                        },
                        "url_context": {
                            "mode": "chose"
                        },
                        "code_execution": {
                            "mode": "chose"
                        },
                        "safetySettings": {
                            "mode": "chose"
                        },
                        "generationConfig": {
                            "nested": {
                                "stopSequences": {
                                    "mode": "chose"
                                },
                                "thinkingConfig": {
                                    "nested": {
                                        "include_thoughts": {
                                            "mode": "chose"
                                        }
                                    }
                                },
                                "responseMimeType": {
                                    "mode": "chose",
                                    "label": "Empty"
                                },
                                "responseModalities": {
                                    "mode": "chose"
                                }
                            }
                        },
                        "system_instruction": {
                            "nested": {
                                "parts": {
                                    "mode": "chose"
                                }
                            }
                        },
                        "google_search_context": {
                            "mode": "chose"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "gemini-ai-q9zyjp"
                            },
                            "label": "My Gemini AI connection"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:gemini-ai-q9zyjp",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "model",
                        "type": "select",
                        "label": "AI Model",
                        "required": true
                    },
                    {
                        "name": "contents",
                        "spec": [
                            {
                                "name": "role",
                                "type": "select",
                                "label": "Role",
                                "options": [
                                    {
                                        "label": "User",
                                        "value": "user"
                                    },
                                    {
                                        "label": "Model",
                                        "value": "model"
                                    }
                                ]
                            },
                            {
                                "name": "parts",
                                "spec": [
                                    {
                                        "name": "type",
                                        "type": "select",
                                        "label": "Message Type",
                                        "options": [
                                            {
                                                "label": "Text",
                                                "value": "text",
                                                "nested": [
                                                    {
                                                        "name": "text",
                                                        "type": "text",
                                                        "label": "Text",
                                                        "required": false
                                                    }
                                                ]
                                            },
                                            {
                                                "label": "File",
                                                "value": "file",
                                                "nested": [
                                                    {
                                                        "name": "file_data",
                                                        "spec": [
                                                            {
                                                                "name": "mime_type",
                                                                "type": "text",
                                                                "label": "Mime Type",
                                                                "required": false
                                                            },
                                                            {
                                                                "help": "You get this from the 'Upload a File' module.",
                                                                "name": "file_uri",
                                                                "type": "text",
                                                                "label": "File URI",
                                                                "required": false
                                                            }
                                                        ],
                                                        "type": "collection",
                                                        "label": "File Data",
                                                        "required": false
                                                    }
                                                ]
                                            }
                                        ]
                                    }
                                ],
                                "type": "array",
                                "label": "Parts"
                            }
                        ],
                        "type": "array",
                        "label": "Messages",
                        "required": true
                    },
                    {
                        "name": "system_instruction",
                        "spec": [
                            {
                                "name": "parts",
                                "spec": {
                                    "name": "value",
                                    "spec": [
                                        {
                                            "name": "text",
                                            "type": "text",
                                            "label": "Value"
                                        }
                                    ],
                                    "type": "collection",
                                    "label": "Prompt"
                                },
                                "type": "array",
                                "label": "Prompts"
                            }
                        ],
                        "type": "collection",
                        "label": "System Instructions"
                    },
                    {
                        "name": "safetySettings",
                        "spec": [
                            {
                                "name": "category",
                                "type": "select",
                                "label": "Category",
                                "options": [
                                    {
                                        "label": "Harassment content",
                                        "value": "HARM_CATEGORY_HARASSMENT"
                                    },
                                    {
                                        "label": "Hate speech and content",
                                        "value": "HARM_CATEGORY_HATE_SPEECH"
                                    },
                                    {
                                        "label": "Sexually explicit content.",
                                        "value": "HARM_CATEGORY_SEXUALLY_EXPLICIT"
                                    },
                                    {
                                        "label": "Dangerous content:",
                                        "value": "HARM_CATEGORY_DANGEROUS_CONTENT"
                                    }
                                ]
                            },
                            {
                                "name": "threshold",
                                "type": "select",
                                "label": "Threshold",
                                "options": [
                                    {
                                        "label": "Block low and above.",
                                        "value": "BLOCK_LOW_AND_ABOVE"
                                    },
                                    {
                                        "label": "Block medium and above.",
                                        "value": "BLOCK_MEDIUM_AND_ABOVE"
                                    },
                                    {
                                        "label": "Block only high.",
                                        "value": "BLOCK_ONLY_HIGH"
                                    },
                                    {
                                        "label": "Block none.",
                                        "value": "BLOCK_NONE"
                                    }
                                ]
                            }
                        ],
                        "type": "array",
                        "label": "Safety Settings"
                    },
                    {
                        "name": "generationConfig",
                        "spec": [
                            {
                                "name": "stopSequences",
                                "spec": {
                                    "name": "value",
                                    "type": "text",
                                    "label": "Stop Sequence"
                                },
                                "type": "array",
                                "label": "Stop Sequences"
                            },
                            {
                                "name": "responseModalities",
                                "type": "select",
                                "label": "Response Modalities",
                                "multiple": true,
                                "validate": {
                                    "enum": [
                                        "text",
                                        "image"
                                    ]
                                }
                            },
                            {
                                "name": "maxOutputTokens",
                                "type": "number",
                                "label": "Max Output Tokens"
                            },
                            {
                                "name": "temperature",
                                "type": "number",
                                "label": "Temperature",
                                "validate": {
                                    "max": 1,
                                    "min": 0
                                }
                            },
                            {
                                "name": "topP",
                                "type": "number",
                                "label": "Top P",
                                "validate": {
                                    "max": 1,
                                    "min": 0
                                }
                            },
                            {
                                "name": "topK",
                                "type": "number",
                                "label": "Top K"
                            },
                            {
                                "name": "thinkingConfig",
                                "spec": [
                                    {
                                        "name": "thinkingBudget",
                                        "type": "number",
                                        "label": "Thinking Budget"
                                    },
                                    {
                                        "name": "include_thoughts",
                                        "type": "boolean",
                                        "label": "Include Thoughts"
                                    }
                                ],
                                "type": "collection",
                                "label": "Thinking Config"
                            },
                            {
                                "name": "responseMimeType",
                                "type": "select",
                                "label": "Response Mime Type"
                            }
                        ],
                        "type": "collection",
                        "label": "Generation configurations"
                    },
                    {
                        "name": "tools",
                        "type": "any",
                        "label": "Tools"
                    },
                    {
                        "name": "tool_config",
                        "type": "any",
                        "label": "Tool Config"
                    },
                    {
                        "name": "url_context",
                        "type": "boolean",
                        "label": "URL Context"
                    },
                    {
                        "name": "google_search_context",
                        "type": "boolean",
                        "label": "Google Search Grounding"
                    },
                    {
                        "name": "code_execution",
                        "type": "boolean",
                        "label": "Code Execution"
                    }
                ]
            }
        },
        {
            "id": 8,
            "module": "json:ParseJSON",
            "version": 1,
            "parameters": {
                "type": 160949
            },
            "mapper": {
                "json": "{{14.result}}"
            },
            "metadata": {
                "designer": {
                    "x": 904,
                    "y": 10
                },
                "restore": {
                    "parameters": {
                        "type": {
                            "label": "My data structure"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "type",
                        "type": "udt",
                        "label": "Data structure"
                    }
                ],
                "expect": [
                    {
                        "name": "json",
                        "type": "text",
                        "label": "JSON string",
                        "required": true
                    }
                ],
                "interface": [
                    {
                        "help": "",
                        "name": "Nombre",
                        "type": "text",
                        "label": "Nombre",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "Apellido",
                        "type": "text",
                        "label": "Apellido",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "Correo",
                        "type": "text",
                        "label": "Correo",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "País",
                        "type": "text",
                        "label": "País",
                        "default": null,
                        "required": false,
                        "multiline": false
                    }
                ]
            }
        },
        {
            "id": 7,
            "module": "google-sheets:addRow",
            "version": 2,
            "parameters": {
                "__IMTCONN__": 4420089
            },
            "mapper": {
                "from": "drive",
                "mode": "select",
                "values": {
                    "0": "{{8.Correo}}",
                    "1": "{{8.Nombre}}",
                    "2": "{{8.Apellido}}",
                    "3": "{{8.`Teléfono`}}",
                    "4": "{{1.date}}"
                },
                "sheetId": "Uplin Community",
                "spreadsheetId": "/1q9F0L243aTR_3iFLksq0i_iwEE1G92EI/1t6GYCTZJ2QdlU-4rELs2Mn79-xFAKZrG/1kSFZn4bsTvLWx2eRnBa9TwR6RrTKeMx9/1Lh4hMBnwL_PpqA2m1y6ZahM3EFVsAmtQjZSm5WtGnKA",
                "includesHeaders": true,
                "insertDataOption": "INSERT_ROWS",
                "valueInputOption": "USER_ENTERED",
                "insertUnformatted": false
            },
            "metadata": {
                "designer": {
                    "x": 1302,
                    "y": 14
                },
                "restore": {
                    "expect": {
                        "from": {
                            "label": "My Drive"
                        },
                        "mode": {
                            "label": "Search by path"
                        },
                        "sheetId": {
                            "label": "Uplin Community"
                        },
                        "spreadsheetId": {
                            "path": [
                                "VACANTES",
                                "ADMINISTRACION DE VACANTES EN UPLIN CAREERS",
                                "BASE DE DATOS GENERAL",
                                "Base de datos de perfiles UPLIN"
                            ]
                        },
                        "includesHeaders": {
                            "label": "Yes",
                            "nested": [
                                {
                                    "name": "values",
                                    "spec": [
                                        {
                                            "name": "0",
                                            "type": "text",
                                            "label": "Correo electrónico (A)"
                                        },
                                        {
                                            "name": "1",
                                            "type": "text",
                                            "label": "Nombre (B)"
                                        },
                                        {
                                            "name": "2",
                                            "type": "text",
                                            "label": "Apellido (C)"
                                        },
                                        {
                                            "name": "3",
                                            "type": "text",
                                            "label": "Teléfono (D)"
                                        },
                                        {
                                            "name": "4",
                                            "type": "text",
                                            "label": "(E)"
                                        },
                                        {
                                            "name": "5",
                                            "type": "text",
                                            "label": "(F)"
                                        },
                                        {
                                            "name": "6",
                                            "type": "text",
                                            "label": "(G)"
                                        },
                                        {
                                            "name": "7",
                                            "type": "text",
                                            "label": "(H)"
                                        },
                                        {
                                            "name": "8",
                                            "type": "text",
                                            "label": "(I)"
                                        },
                                        {
                                            "name": "9",
                                            "type": "text",
                                            "label": "(J)"
                                        },
                                        {
                                            "name": "10",
                                            "type": "text",
                                            "label": "(K)"
                                        },
                                        {
                                            "name": "11",
                                            "type": "text",
                                            "label": "(L)"
                                        },
                                        {
                                            "name": "12",
                                            "type": "text",
                                            "label": "(M)"
                                        },
                                        {
                                            "name": "13",
                                            "type": "text",
                                            "label": "(N)"
                                        },
                                        {
                                            "name": "14",
                                            "type": "text",
                                            "label": "(O)"
                                        },
                                        {
                                            "name": "15",
                                            "type": "text",
                                            "label": "(P)"
                                        },
                                        {
                                            "name": "16",
                                            "type": "text",
                                            "label": "(Q)"
                                        },
                                        {
                                            "name": "17",
                                            "type": "text",
                                            "label": "(R)"
                                        },
                                        {
                                            "name": "18",
                                            "type": "text",
                                            "label": "(S)"
                                        },
                                        {
                                            "name": "19",
                                            "type": "text",
                                            "label": "(T)"
                                        },
                                        {
                                            "name": "20",
                                            "type": "text",
                                            "label": "(U)"
                                        },
                                        {
                                            "name": "21",
                                            "type": "text",
                                            "label": "(V)"
                                        },
                                        {
                                            "name": "22",
                                            "type": "text",
                                            "label": "(W)"
                                        },
                                        {
                                            "name": "23",
                                            "type": "text",
                                            "label": "(X)"
                                        },
                                        {
                                            "name": "24",
                                            "type": "text",
                                            "label": "(Y)"
                                        },
                                        {
                                            "name": "25",
                                            "type": "text",
                                            "label": "(Z)"
                                        }
                                    ],
                                    "type": "collection",
                                    "label": "Values"
                                }
                            ]
                        },
                        "insertDataOption": {
                            "mode": "chose",
                            "label": "Insert rows"
                        },
                        "valueInputOption": {
                            "mode": "chose",
                            "label": "User entered"
                        },
                        "insertUnformatted": {
                            "mode": "chose"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "google"
                            },
                            "label": "My Google connection (uplinrh.master@gmail.com)"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:google",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "mode",
                        "type": "select",
                        "label": "Search Method",
                        "required": true,
                        "validate": {
                            "enum": [
                                "select",
                                "fromAll",
                                "map"
                            ]
                        }
                    },
                    {
                        "name": "insertUnformatted",
                        "type": "boolean",
                        "label": "Unformatted",
                        "required": true
                    },
                    {
                        "name": "valueInputOption",
                        "type": "select",
                        "label": "Value input option",
                        "validate": {
                            "enum": [
                                "USER_ENTERED",
                                "RAW"
                            ]
                        }
                    },
                    {
                        "name": "insertDataOption",
                        "type": "select",
                        "label": "Insert data option",
                        "validate": {
                            "enum": [
                                "INSERT_ROWS",
                                "OVERWRITE"
                            ]
                        }
                    },
                    {
                        "name": "from",
                        "type": "select",
                        "label": "Drive",
                        "required": true,
                        "validate": {
                            "enum": [
                                "drive",
                                "share",
                                "team"
                            ]
                        }
                    },
                    {
                        "name": "spreadsheetId",
                        "type": "file",
                        "label": "Spreadsheet ID",
                        "required": true
                    },
                    {
                        "name": "sheetId",
                        "type": "select",
                        "label": "Sheet Name",
                        "required": true
                    },
                    {
                        "name": "includesHeaders",
                        "type": "select",
                        "label": "Table contains headers",
                        "required": true,
                        "validate": {
                            "enum": [
                                true,
                                false
                            ]
                        }
                    },
                    {
                        "name": "values",
                        "spec": [
                            {
                                "name": "0",
                                "type": "text",
                                "label": "Correo electrónico (A)"
                            },
                            {
                                "name": "1",
                                "type": "text",
                                "label": "Nombre (B)"
                            },
                            {
                                "name": "2",
                                "type": "text",
                                "label": "Apellido (C)"
                            },
                            {
                                "name": "3",
                                "type": "text",
                                "label": "Teléfono (D)"
                            },
                            {
                                "name": "4",
                                "type": "text",
                                "label": "(E)"
                            },
                            {
                                "name": "5",
                                "type": "text",
                                "label": "(F)"
                            },
                            {
                                "name": "6",
                                "type": "text",
                                "label": "(G)"
                            },
                            {
                                "name": "7",
                                "type": "text",
                                "label": "(H)"
                            },
                            {
                                "name": "8",
                                "type": "text",
                                "label": "(I)"
                            },
                            {
                                "name": "9",
                                "type": "text",
                                "label": "(J)"
                            },
                            {
                                "name": "10",
                                "type": "text",
                                "label": "(K)"
                            },
                            {
                                "name": "11",
                                "type": "text",
                                "label": "(L)"
                            },
                            {
                                "name": "12",
                                "type": "text",
                                "label": "(M)"
                            },
                            {
                                "name": "13",
                                "type": "text",
                                "label": "(N)"
                            },
                            {
                                "name": "14",
                                "type": "text",
                                "label": "(O)"
                            },
                            {
                                "name": "15",
                                "type": "text",
                                "label": "(P)"
                            },
                            {
                                "name": "16",
                                "type": "text",
                                "label": "(Q)"
                            },
                            {
                                "name": "17",
                                "type": "text",
                                "label": "(R)"
                            },
                            {
                                "name": "18",
                                "type": "text",
                                "label": "(S)"
                            },
                            {
                                "name": "19",
                                "type": "text",
                                "label": "(T)"
                            },
                            {
                                "name": "20",
                                "type": "text",
                                "label": "(U)"
                            },
                            {
                                "name": "21",
                                "type": "text",
                                "label": "(V)"
                            },
                            {
                                "name": "22",
                                "type": "text",
                                "label": "(W)"
                            },
                            {
                                "name": "23",
                                "type": "text",
                                "label": "(X)"
                            },
                            {
                                "name": "24",
                                "type": "text",
                                "label": "(Y)"
                            },
                            {
                                "name": "25",
                                "type": "text",
                                "label": "(Z)"
                            }
                        ],
                        "type": "collection",
                        "label": "Values"
                    }
                ],
                "advanced": true
            }
        },
        {
            "id": 11,
            "module": "google-email:ActionMoveEmail",
            "version": 2,
            "parameters": {
                "account": 4854887
            },
            "mapper": {
                "id": "{{1.id}}",
                "folder": "INBOX",
                "destinationFolder": "[Gmail]/Todos"
            },
            "metadata": {
                "designer": {
                    "x": 1800,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "folder": {
                            "mode": "chose",
                            "path": [
                                "INBOX"
                            ]
                        },
                        "destinationFolder": {
                            "mode": "chose",
                            "path": [
                                "Todos",
                                "Todos"
                            ]
                        }
                    },
                    "parameters": {
                        "account": {
                            "data": {
                                "scoped": "true",
                                "connection": "google-restricted"
                            },
                            "label": "My Google Restricted connection (uplinrh.master@gmail.com)"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "account",
                        "type": "account:google-restricted",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "folder",
                        "type": "folder",
                        "label": "Folder",
                        "required": true
                    },
                    {
                        "name": "destinationFolder",
                        "type": "folder",
                        "label": "Destination folder",
                        "required": true
                    },
                    {
                        "name": "id",
                        "type": "number",
                        "label": "Email ID (UID)",
                        "required": true
                    }
                ]
            }
        }
    ],
    "metadata": {
        "instant": false,
        "version": 1,
        "scenario": {
            "roundtrips": 1,
            "maxErrors": 3,
            "autoCommit": true,
            "autoCommitTriggerLast": true,
            "sequential": false,
            "slots": null,
            "confidential": false,
            "dataloss": false,
            "dlq": false,
            "freshVariables": false
        },
        "designer": {
            "orphans": []
        },
        "zone": "us2.make.com",
        "notes": []
    }
}
```

</details>

---

## Escenario base 2: Limpieza con Text Pattern + Parse JSON  

Plantilla aplicada a otros procesos donde bastaba con **limpieza regex** y **parseo estructurado** sin usar IA.  
- **Fuente:** correos con confirmaciones/altas en bruto.  
- **Transformación:**  
  - **Text Pattern** para aislar el bloque `{ ... }` y remover lo no deseado.  
  - **Parse JSON** para convertirlo en campos personalizables (listos para bases/tablas).  
- **Carga:** inserción en Google Sheets para reporting y cruces posteriores.  

### Imagen del canvas  
![Escenario ETL con Text Pattern y Parse JSON](./img/escenario%20etl%20con%20text%20pattern%20y%20parse%20json.jpeg)

### Blueprint del escenario (JSON)  
<details>
<summary>Ver código JSON</summary>

```json
{
    "name": "CLIENTES B2B (sin IA)",
    "flow": [
        {
            "id": 1,
            "module": "google-email:TriggerNewEmail",
            "version": 2,
            "parameters": {
                "folder": "INBOX",
                "xGmRaw": "from:contacto@uplinhr.com subject:\"NUEVA COMPRA PLAN PLATINUM\"",
                "account": 4854887,
                "markSeen": true,
                "maxResults": 100,
                "searchType": "gmail"
            },
            "mapper": {},
            "metadata": {
                "designer": {
                    "x": 0,
                    "y": 0
                },
                "restore": {
                    "parameters": {
                        "folder": {
                            "path": [
                                "INBOX"
                            ]
                        },
                        "account": {
                            "data": {
                                "scoped": "true",
                                "connection": "google-restricted"
                            },
                            "label": "My Google Restricted connection (uplinrh.master@gmail.com)"
                        },
                        "searchType": {
                            "label": "Gmail filter"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "account",
                        "type": "account:google-restricted",
                        "label": "Connection",
                        "required": true
                    },
                    {
                        "name": "searchType",
                        "type": "select",
                        "label": "Filter type",
                        "required": true,
                        "validate": {
                            "enum": [
                                "simple",
                                "gmail"
                            ]
                        }
                    },
                    {
                        "name": "markSeen",
                        "type": "boolean",
                        "label": "Mark email message(s) as read when fetched"
                    },
                    {
                        "name": "maxResults",
                        "type": "uinteger",
                        "label": "Maximum number of results",
                        "required": true
                    },
                    {
                        "name": "folder",
                        "type": "folder",
                        "label": "Folder",
                        "required": true
                    },
                    {
                        "name": "xGmRaw",
                        "type": "text",
                        "label": "Query",
                        "required": true
                    }
                ]
            }
        },
        {
            "id": 10,
            "module": "regexp:Parser",
            "version": 1,
            "parameters": {
                "global": false,
                "pattern": "(\\{[\\s\\S]*?\\})",
                "multiline": false,
                "sensitive": true,
                "singleline": false,
                "continueWhenNoRes": false
            },
            "mapper": {
                "text": "{{1.text}}"
            },
            "metadata": {
                "designer": {
                    "x": 300,
                    "y": 0
                },
                "restore": {},
                "parameters": [
                    {
                        "name": "pattern",
                        "type": "text",
                        "label": "Pattern",
                        "required": true
                    },
                    {
                        "name": "global",
                        "type": "boolean",
                        "label": "Global match",
                        "required": true
                    },
                    {
                        "name": "sensitive",
                        "type": "boolean",
                        "label": "Case sensitive",
                        "required": true
                    },
                    {
                        "name": "multiline",
                        "type": "boolean",
                        "label": "Multiline",
                        "required": true
                    },
                    {
                        "name": "singleline",
                        "type": "boolean",
                        "label": "Singleline",
                        "required": true
                    },
                    {
                        "name": "continueWhenNoRes",
                        "type": "boolean",
                        "label": "Continue the execution of the route even if the module finds no matches",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "text",
                        "type": "text",
                        "label": "Text"
                    }
                ],
                "interface": [
                    {
                        "name": "$1",
                        "type": "text",
                        "label": "$1"
                    }
                ]
            }
        },
        {
            "id": 8,
            "module": "json:ParseJSON",
            "version": 1,
            "parameters": {
                "type": 160949
            },
            "mapper": {
                "json": "{{10.`$1`}}"
            },
            "metadata": {
                "designer": {
                    "x": 600,
                    "y": 0
                },
                "restore": {
                    "parameters": {
                        "type": {
                            "label": "My data structure"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "type",
                        "type": "udt",
                        "label": "Data structure"
                    }
                ],
                "expect": [
                    {
                        "name": "json",
                        "type": "text",
                        "label": "JSON string",
                        "required": true
                    }
                ],
                "interface": [
                    {
                        "help": "",
                        "name": "Nombre",
                        "type": "text",
                        "label": "Nombre",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "Apellido",
                        "type": "text",
                        "label": "Apellido",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "Correo",
                        "type": "text",
                        "label": "Correo",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "País",
                        "type": "text",
                        "label": "País",
                        "default": null,
                        "required": false,
                        "multiline": false
                    }
                ]
            }
        },
        {
            "id": 7,
            "module": "google-sheets:addRow",
            "version": 2,
            "parameters": {
                "__IMTCONN__": 4420089
            },
            "mapper": {
                "from": "drive",
                "mode": "select",
                "values": {
                    "0": "{{8.Correo}}",
                    "1": "{{8.Nombre}}",
                    "2": "{{8.Apellido}}",
                    "3": "{{8.`País`}}",
                    "4": "{{1.date}}",
                    "5": "{{8.`Código Postal`}}",
                    "6": "{{8.Ciudad}}",
                    "7": "{{8.`Dirección`}}",
                    "9": "{{8.IVA}}"
                },
                "sheetId": "PLAN PLATINUM",
                "spreadsheetId": "/1JoxjvX80umc_JBMntveuIAfejGXHSb8w/13Rmq7dVbJ-U5Utqh3Xdzb9KKPmnXhZ_Of4EHUFAWMgM",
                "includesHeaders": true,
                "insertDataOption": "INSERT_ROWS",
                "valueInputOption": "USER_ENTERED",
                "insertUnformatted": false
            },
            "metadata": {
                "designer": {
                    "x": 900,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "from": {
                            "label": "My Drive"
                        },
                        "mode": {
                            "label": "Search by path"
                        },
                        "sheetId": {
                            "label": "PLAN PLATINUM"
                        },
                        "spreadsheetId": {
                            "path": [
                                "CLIENTES B2B",
                                "CLIENTES"
                            ]
                        },
                        "includesHeaders": {
                            "label": "Yes",
                            "nested": [
                                {
                                    "name": "values",
                                    "spec": [
                                        {
                                            "name": "0",
                                            "type": "text",
                                            "label": "Correo electrónico (A)"
                                        },
                                        {
                                            "name": "1",
                                            "type": "text",
                                            "label": "Nombre (B)"
                                        },
                                        {
                                            "name": "2",
                                            "type": "text",
                                            "label": "Apellido (C)"
                                        },
                                        {
                                            "name": "3",
                                            "type": "text",
                                            "label": "Código País (D)"
                                        },
                                        {
                                            "name": "4",
                                            "type": "text",
                                            "label": "Marca Temporal (E)"
                                        },
                                        {
                                            "name": "5",
                                            "type": "text",
                                            "label": "Código Postal (F)"
                                        },
                                        {
                                            "name": "6",
                                            "type": "text",
                                            "label": "Ciudad (G)"
                                        },
                                        {
                                            "name": "7",
                                            "type": "text",
                                            "label": "Dirección (H)"
                                        },
                                        {
                                            "name": "8",
                                            "type": "text",
                                            "label": "País (I)"
                                        },
                                        {
                                            "name": "9",
                                            "type": "text",
                                            "label": "Identificación Tributaria (J)"
                                        },
                                        {
                                            "name": "10",
                                            "type": "text",
                                            "label": "Fecha y hora de inscripción (K)"
                                        },
                                        {
                                            "name": "11",
                                            "type": "text",
                                            "label": "(L)"
                                        },
                                        {
                                            "name": "12",
                                            "type": "text",
                                            "label": "(M)"
                                        },
                                        {
                                            "name": "13",
                                            "type": "text",
                                            "label": "(N)"
                                        },
                                        {
                                            "name": "14",
                                            "type": "text",
                                            "label": "(O)"
                                        },
                                        {
                                            "name": "15",
                                            "type": "text",
                                            "label": "(P)"
                                        },
                                        {
                                            "name": "16",
                                            "type": "text",
                                            "label": "(Q)"
                                        },
                                        {
                                            "name": "17",
                                            "type": "text",
                                            "label": "(R)"
                                        },
                                        {
                                            "name": "18",
                                            "type": "text",
                                            "label": "(S)"
                                        },
                                        {
                                            "name": "19",
                                            "type": "text",
                                            "label": "(T)"
                                        },
                                        {
                                            "name": "20",
                                            "type": "text",
                                            "label": "(U)"
                                        },
                                        {
                                            "name": "21",
                                            "type": "text",
                                            "label": "(V)"
                                        },
                                        {
                                            "name": "22",
                                            "type": "text",
                                            "label": "(W)"
                                        },
                                        {
                                            "name": "23",
                                            "type": "text",
                                            "label": "(X)"
                                        },
                                        {
                                            "name": "24",
                                            "type": "text",
                                            "label": "(Y)"
                                        },
                                        {
                                            "name": "25",
                                            "type": "text",
                                            "label": "(Z)"
                                        },
                                        {
                                            "name": "26",
                                            "type": "text",
                                            "label": "(AA)"
                                        },
                                        {
                                            "name": "27",
                                            "type": "text",
                                            "label": "(AB)"
                                        },
                                        {
                                            "name": "28",
                                            "type": "text",
                                            "label": "(AC)"
                                        },
                                        {
                                            "name": "29",
                                            "type": "text",
                                            "label": "(AD)"
                                        },
                                        {
                                            "name": "30",
                                            "type": "text",
                                            "label": "(AE)"
                                        }
                                    ],
                                    "type": "collection",
                                    "label": "Values"
                                }
                            ]
                        },
                        "insertDataOption": {
                            "mode": "chose",
                            "label": "Insert rows"
                        },
                        "valueInputOption": {
                            "mode": "chose",
                            "label": "User entered"
                        },
                        "insertUnformatted": {
                            "mode": "chose"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "google"
                            },
                            "label": "My Google connection (uplinrh.master@gmail.com)"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:google",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "mode",
                        "type": "select",
                        "label": "Search Method",
                        "required": true,
                        "validate": {
                            "enum": [
                                "select",
                                "fromAll",
                                "map"
                            ]
                        }
                    },
                    {
                        "name": "insertUnformatted",
                        "type": "boolean",
                        "label": "Unformatted",
                        "required": true
                    },
                    {
                        "name": "valueInputOption",
                        "type": "select",
                        "label": "Value input option",
                        "validate": {
                            "enum": [
                                "USER_ENTERED",
                                "RAW"
                            ]
                        }
                    },
                    {
                        "name": "insertDataOption",
                        "type": "select",
                        "label": "Insert data option",
                        "validate": {
                            "enum": [
                                "INSERT_ROWS",
                                "OVERWRITE"
                            ]
                        }
                    },
                    {
                        "name": "from",
                        "type": "select",
                        "label": "Drive",
                        "required": true,
                        "validate": {
                            "enum": [
                                "drive",
                                "share",
                                "team"
                            ]
                        }
                    },
                    {
                        "name": "spreadsheetId",
                        "type": "file",
                        "label": "Spreadsheet ID",
                        "required": true
                    },
                    {
                        "name": "sheetId",
                        "type": "select",
                        "label": "Sheet Name",
                        "required": true
                    },
                    {
                        "name": "includesHeaders",
                        "type": "select",
                        "label": "Table contains headers",
                        "required": true,
                        "validate": {
                            "enum": [
                                true,
                                false
                            ]
                        }
                    },
                    {
                        "name": "values",
                        "spec": [
                            {
                                "name": "0",
                                "type": "text",
                                "label": "Correo electrónico (A)"
                            },
                            {
                                "name": "1",
                                "type": "text",
                                "label": "Nombre (B)"
                            },
                            {
                                "name": "2",
                                "type": "text",
                                "label": "Apellido (C)"
                            },
                            {
                                "name": "3",
                                "type": "text",
                                "label": "Código País (D)"
                            },
                            {
                                "name": "4",
                                "type": "text",
                                "label": "Marca Temporal (E)"
                            },
                            {
                                "name": "5",
                                "type": "text",
                                "label": "Código Postal (F)"
                            },
                            {
                                "name": "6",
                                "type": "text",
                                "label": "Ciudad (G)"
                            },
                            {
                                "name": "7",
                                "type": "text",
                                "label": "Dirección (H)"
                            },
                            {
                                "name": "8",
                                "type": "text",
                                "label": "País (I)"
                            },
                            {
                                "name": "9",
                                "type": "text",
                                "label": "Identificación Tributaria (J)"
                            },
                            {
                                "name": "10",
                                "type": "text",
                                "label": "Fecha y hora de inscripción (K)"
                            },
                            {
                                "name": "11",
                                "type": "text",
                                "label": "(L)"
                            },
                            {
                                "name": "12",
                                "type": "text",
                                "label": "(M)"
                            },
                            {
                                "name": "13",
                                "type": "text",
                                "label": "(N)"
                            },
                            {
                                "name": "14",
                                "type": "text",
                                "label": "(O)"
                            },
                            {
                                "name": "15",
                                "type": "text",
                                "label": "(P)"
                            },
                            {
                                "name": "16",
                                "type": "text",
                                "label": "(Q)"
                            },
                            {
                                "name": "17",
                                "type": "text",
                                "label": "(R)"
                            },
                            {
                                "name": "18",
                                "type": "text",
                                "label": "(S)"
                            },
                            {
                                "name": "19",
                                "type": "text",
                                "label": "(T)"
                            },
                            {
                                "name": "20",
                                "type": "text",
                                "label": "(U)"
                            },
                            {
                                "name": "21",
                                "type": "text",
                                "label": "(V)"
                            },
                            {
                                "name": "22",
                                "type": "text",
                                "label": "(W)"
                            },
                            {
                                "name": "23",
                                "type": "text",
                                "label": "(X)"
                            },
                            {
                                "name": "24",
                                "type": "text",
                                "label": "(Y)"
                            },
                            {
                                "name": "25",
                                "type": "text",
                                "label": "(Z)"
                            },
                            {
                                "name": "26",
                                "type": "text",
                                "label": "(AA)"
                            },
                            {
                                "name": "27",
                                "type": "text",
                                "label": "(AB)"
                            },
                            {
                                "name": "28",
                                "type": "text",
                                "label": "(AC)"
                            },
                            {
                                "name": "29",
                                "type": "text",
                                "label": "(AD)"
                            },
                            {
                                "name": "30",
                                "type": "text",
                                "label": "(AE)"
                            }
                        ],
                        "type": "collection",
                        "label": "Values"
                    }
                ],
                "advanced": true
            }
        },
        {
            "id": 11,
            "module": "google-email:ActionMoveEmail",
            "version": 2,
            "parameters": {
                "account": 4854887
            },
            "mapper": {
                "id": "{{1.id}}",
                "folder": "INBOX",
                "destinationFolder": "[Gmail]/Todos"
            },
            "metadata": {
                "designer": {
                    "x": 1200,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "folder": {
                            "mode": "chose",
                            "path": [
                                "INBOX"
                            ]
                        },
                        "destinationFolder": {
                            "mode": "chose",
                            "path": [
                                "Todos",
                                "Todos"
                            ]
                        }
                    },
                    "parameters": {
                        "account": {
                            "data": {
                                "scoped": "true",
                                "connection": "google-restricted"
                            },
                            "label": "My Google Restricted connection (uplinrh.master@gmail.com)"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "account",
                        "type": "account:google-restricted",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "folder",
                        "type": "folder",
                        "label": "Folder",
                        "required": true
                    },
                    {
                        "name": "destinationFolder",
                        "type": "folder",
                        "label": "Destination folder",
                        "required": true
                    },
                    {
                        "name": "id",
                        "type": "number",
                        "label": "Email ID (UID)",
                        "required": true
                    }
                ]
            }
        }
    ],
    "metadata": {
        "instant": false,
        "version": 1,
        "scenario": {
            "roundtrips": 1,
            "maxErrors": 3,
            "autoCommit": true,
            "autoCommitTriggerLast": true,
            "sequential": false,
            "slots": null,
            "confidential": false,
            "dataloss": false,
            "dlq": false,
            "freshVariables": false
        },
        "designer": {
            "orphans": []
        },
        "zone": "us2.make.com",
        "notes": []
    }
}
```

</details>

---

## Conclusiones  

Estos **escenarios base** permitieron:  
- Automatizar la colecta desde systeme.io vía Gmail.  
- Corregir errores y estandarizar formatos con IA o reglas de texto.  
- Parsear la información a estructuras consumibles.  
- Centralizar en Google Sheets una **fuente de verdad única** para UPLIN HR.
