# Principales Flujos de Automatización en Make  

En esta sección se documentan los flujos principales diseñados en **Make**. Estos escenarios fueron creados como **bases reutilizables** para extraer, transformar y organizar información proveniente de **systeme.io**, y para automatizar tareas críticas en UPLIN HR.  

---

## Extracción de Leads desde systeme.io  

### 1. Inscriptos a Webinars  
Flujo diseñado para capturar automáticamente las inscripciones a webinars gratuitos o pagos:  
- **Fuente:** Gmail (correos de confirmación de inscripción).  
- **Transformación:** extracción de datos JSON contenidos en los correos.  
- **Carga:** inserción en Google Sheets para registro y seguimiento.  

📷 **Imagen del canvas**  
![Escenario inscriptos webinars](./img/escenario%20etl%20con%20text%20pattern%20y%20parse%20json.jpeg)

<details>
<summary>Ver código JSON</summary>

```json
{
    "name": "Registrados a Webinar (versión sin IA)",
    "flow": [
        {
            "id": 1,
            "module": "google-email:TriggerNewEmail",
            "version": 2,
            "parameters": {
                "folder": "INBOX",
                "xGmRaw": "from:contacto@uplinhr.com subject:\"NUEVO CONTACTO REGISTRADO A WEBINAR: NEUROHABILIDADES\"",
                "account": 4854887,
                "markSeen": true,
                "maxResults": 100,
                "searchType": "gmail"
            },
            "mapper": {},
            "metadata": {
                "designer": {
                    "x": 21,
                    "y": -29
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
                    "x": 321,
                    "y": -29
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
                    "x": 628,
                    "y": -26
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
                    "4": "{{1.date}}"
                },
                "sheetId": "WEBINAR NEUROHABILIDADES",
                "spreadsheetId": "/1h5JKn8B268tZukC7pDbVdeBSLMvyu7CX/1oajcAXUZKrW5tSm0VS9FCXhl1FelhDNEfJZhYXDDBsI",
                "includesHeaders": true,
                "insertDataOption": "INSERT_ROWS",
                "valueInputOption": "USER_ENTERED",
                "insertUnformatted": false
            },
            "metadata": {
                "designer": {
                    "x": 882,
                    "y": -35
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
                            "label": "WEBINAR NEUROHABILIDADES"
                        },
                        "spreadsheetId": {
                            "path": [
                                "INSCRIPCION A WEBINARS",
                                "INSCRIPCION A WEBINARS"
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
                                            "label": "País (F)"
                                        },
                                        {
                                            "name": "6",
                                            "type": "text",
                                            "label": "Fecha y hora de inscripción (G)"
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
                                "label": "País (F)"
                            },
                            {
                                "name": "6",
                                "type": "text",
                                "label": "Fecha y hora de inscripción (G)"
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
                    "x": 1182,
                    "y": -35
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

### 2. Inscriptos a Charlas Gratuitas  
Similar al caso anterior, pero aplicado a **charlas gratuitas** ofrecidas como estrategia de captación.  
- **Fuente:** Gmail (con filtros específicos según asunto del correo).  
- **Transformación:** uso de **regexp parser** para aislar el bloque `{...}` y **Parse JSON** para normalizar los campos (nombre, apellido, correo, país).  
- **Carga:** registro en Google Sheets con fecha y hora de inscripción.  

📷 **Imagen del canvas**  
![Escenario inscriptos charlas gratuitas](./img/escenario%20etl%20con%20text%20pattern%20y%20parse%20json.jpeg)

<details>
<summary>Ver código JSON</summary>

```json
{
    "name": "Registrados a Charlas grabadas (sin IA - Madres y Líderes)",
    "flow": [
        {
            "id": 1,
            "module": "google-email:TriggerNewEmail",
            "version": 2,
            "parameters": {
                "folder": "INBOX",
                "xGmRaw": "from:contacto@uplinhr.com subject:\"NUEVO USUARIO: CHARLA GRATUITA SINDROME DEL IMPOSTOR\"",
                "account": 4854887,
                "markSeen": true,
                "maxResults": 100,
                "searchType": "gmail"
            },
            "mapper": {},
            "metadata": {
                "designer": {
                    "x": 21,
                    "y": -29
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
                    "x": 321,
                    "y": -29
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
                    "x": 628,
                    "y": -26
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
                    "4": "{{1.date}}"
                },
                "sheetId": "CHARLA GRATUITA SINDROME IMPOSTOR",
                "spreadsheetId": "/1h5JKn8B268tZukC7pDbVdeBSLMvyu7CX/1oajcAXUZKrW5tSm0VS9FCXhl1FelhDNEfJZhYXDDBsI",
                "includesHeaders": true,
                "insertDataOption": "INSERT_ROWS",
                "valueInputOption": "USER_ENTERED",
                "insertUnformatted": false
            },
            "metadata": {
                "designer": {
                    "x": 882,
                    "y": -35
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
                            "label": "CHARLA GRATUITA SINDROME IMPOSTOR"
                        },
                        "spreadsheetId": {
                            "path": [
                                "INSCRIPCION A WEBINARS",
                                "INSCRIPCION A WEBINARS"
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
                                            "label": "País (F)"
                                        },
                                        {
                                            "name": "6",
                                            "type": "text",
                                            "label": "Fecha y hora de inscripción (G)"
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
                                        },
                                        {
                                            "name": "26",
                                            "type": "text",
                                            "label": "(AA)"
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
                                "label": "País (F)"
                            },
                            {
                                "name": "6",
                                "type": "text",
                                "label": "Fecha y hora de inscripción (G)"
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
                            },
                            {
                                "name": "26",
                                "type": "text",
                                "label": "(AA)"
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
                    "x": 1182,
                    "y": -35
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

### 3. Carga de Clientes Nuevos (Compras)  
Flujo orientado a registrar clientes que realizaron la compra de un servicio (ejemplo: **Plan Platinum**).  
- **Fuente:** correos de confirmación de compra.  
- **Transformación:** limpieza con regex y parseo en JSON para extraer datos fiscales (nombre, apellido, correo, dirección, código postal, IVA).  
- **Carga:** inserción en Google Sheets para seguimiento administrativo y facturación.  

📷 **Imagen del canvas**  
![Escenario clientes nuevos](./img/escenario%20etl%20con%20text%20pattern%20y%20parse%20json.jpeg)

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

## Automatización de Tareas  

### Revisión Automatizada de Currículums  
Flujo creado para analizar CVs cargados por candidatos a través de formularios en Google Forms/Sheets:  
- **Fuente:** Google Sheets (respuestas de formulario con archivos adjuntos).  
- **Transformación:** descarga de CVs desde Google Drive, conversión a texto con **PDF.co** y análisis posterior.  
- **Carga:** escritura de observaciones/resumen en la misma hoja para agilizar la evaluación de perfiles.  

📷 **Imagen del canvas**  
![Escenario revisión CV](./img/escenario%20revision%20cv.jpeg)

<details>
<summary>Ver código JSON</summary>

```json
{
    "name": "Revisión de CV automatizada",
    "flow": [
        {
            "id": 2,
            "module": "google-sheets:filterRows",
            "version": 2,
            "parameters": {
                "__IMTCONN__": 3522692
            },
            "mapper": {
                "from": "drive",
                "filter": [
                    [
                        {
                            "a": "J",
                            "o": "notexist"
                        }
                    ]
                ],
                "sheetId": "Respuestas de formulario 1",
                "sortOrder": "asc",
                "spreadsheetId": "19NFO08IQG0lFvfqp4fg_IdD_GhnuzA_S0xZUEo1o4bo",
                "tableFirstRow": "A1:CZ1",
                "includesHeaders": true,
                "valueRenderOption": "FORMATTED_VALUE",
                "dateTimeRenderOption": "FORMATTED_STRING"
            },
            "metadata": {
                "designer": {
                    "x": 0,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "from": {
                            "label": "Select from My Drive"
                        },
                        "orderBy": {
                            "mode": "chose"
                        },
                        "sheetId": {
                            "mode": "chose",
                            "label": "Respuestas de formulario 1"
                        },
                        "sortOrder": {
                            "mode": "chose",
                            "label": "Ascending"
                        },
                        "spreadsheetId": {
                            "mode": "chose",
                            "label": "CARGA DE CV (Respuestas)"
                        },
                        "tableFirstRow": {
                            "label": "A-CZ"
                        },
                        "includesHeaders": {
                            "mode": "chose",
                            "label": "Yes"
                        },
                        "valueRenderOption": {
                            "mode": "chose",
                            "label": "Formatted value"
                        },
                        "dateTimeRenderOption": {
                            "mode": "chose",
                            "label": "Formatted string"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "google"
                            },
                            "label": "My Google connection (gastorellano@gmail.com)"
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
                        "name": "from",
                        "type": "select",
                        "label": "Search Method",
                        "required": true,
                        "validate": {
                            "enum": [
                                "drive",
                                "share"
                            ]
                        }
                    },
                    {
                        "name": "valueRenderOption",
                        "type": "select",
                        "label": "Value render option",
                        "validate": {
                            "enum": [
                                "FORMATTED_VALUE",
                                "UNFORMATTED_VALUE",
                                "FORMULA"
                            ]
                        }
                    },
                    {
                        "name": "dateTimeRenderOption",
                        "type": "select",
                        "label": "Date and time render option",
                        "validate": {
                            "enum": [
                                "SERIAL_NUMBER",
                                "FORMATTED_STRING"
                            ]
                        }
                    },
                    {
                        "name": "limit",
                        "type": "uinteger",
                        "label": "Limit"
                    },
                    {
                        "name": "spreadsheetId",
                        "type": "select",
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
                        "name": "tableFirstRow",
                        "type": "select",
                        "label": "Column range",
                        "required": true,
                        "validate": {
                            "enum": [
                                "A1:Z1",
                                "A1:BZ1",
                                "A1:CZ1",
                                "A1:DZ1",
                                "A1:MZ1",
                                "A1:ZZ1",
                                "A1:AZZ1",
                                "A1:BZZ1",
                                "A1:CZZ1",
                                "A1:DZZ1",
                                "A1:MZZ1",
                                "A1:ZZZ1"
                            ]
                        }
                    },
                    {
                        "name": "filter",
                        "type": "filter",
                        "label": "Filter",
                        "options": "rpc://google-sheets/2/rpcGetFilterKeys?includesHeaders=true"
                    },
                    {
                        "name": "orderBy",
                        "type": "select",
                        "label": "Order by"
                    },
                    {
                        "name": "sortOrder",
                        "type": "select",
                        "label": "Sort order",
                        "validate": {
                            "enum": [
                                "asc",
                                "desc"
                            ]
                        }
                    }
                ],
                "interface": [
                    {
                        "name": "__IMTLENGTH__",
                        "type": "uinteger",
                        "label": "Total number of bundles"
                    },
                    {
                        "name": "__IMTINDEX__",
                        "type": "uinteger",
                        "label": "Bundle order position"
                    },
                    {
                        "name": "__ROW_NUMBER__",
                        "type": "number",
                        "label": "Row number"
                    },
                    {
                        "name": "__SPREADSHEET_ID__",
                        "type": "text",
                        "label": "Spreadsheet ID"
                    },
                    {
                        "name": "__SHEET__",
                        "type": "text",
                        "label": "Sheet"
                    },
                    {
                        "name": "0",
                        "type": "text",
                        "label": "Marca temporal (A)"
                    },
                    {
                        "name": "1",
                        "type": "text",
                        "label": "Dirección de correo electrónico (B)"
                    },
                    {
                        "name": "2",
                        "type": "text",
                        "label": "Apellido y Nombre (C)"
                    },
                    {
                        "name": "3",
                        "type": "text",
                        "label": "Carga tu hoja de vida o CV (PDF o Word) (D)"
                    },
                    {
                        "name": "4",
                        "type": "text",
                        "label": "Correo electrónico (E)"
                    },
                    {
                        "name": "5",
                        "type": "text",
                        "label": "Localidad, Provincia/Estado, País (Código Postal) (F)"
                    },
                    {
                        "name": "6",
                        "type": "text",
                        "label": "Número de teléfono (G)"
                    },
                    {
                        "name": "7",
                        "type": "text",
                        "label": "Comentarios adicionales (H)"
                    },
                    {
                        "name": "8",
                        "type": "text",
                        "label": "Privacidad de los datos (I)"
                    },
                    {
                        "name": "9",
                        "type": "text",
                        "label": "Resumen (J)"
                    },
                    {
                        "name": "10",
                        "type": "text",
                        "label": "Observaciones (K)"
                    },
                    {
                        "name": "11",
                        "type": "text",
                        "label": "Correo de Bienvenida (L)"
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
                    },
                    {
                        "name": "31",
                        "type": "text",
                        "label": "(AF)"
                    },
                    {
                        "name": "32",
                        "type": "text",
                        "label": "(AG)"
                    },
                    {
                        "name": "33",
                        "type": "text",
                        "label": "(AH)"
                    },
                    {
                        "name": "34",
                        "type": "text",
                        "label": "(AI)"
                    },
                    {
                        "name": "35",
                        "type": "text",
                        "label": "(AJ)"
                    },
                    {
                        "name": "36",
                        "type": "text",
                        "label": "(AK)"
                    },
                    {
                        "name": "37",
                        "type": "text",
                        "label": "(AL)"
                    },
                    {
                        "name": "38",
                        "type": "text",
                        "label": "(AM)"
                    },
                    {
                        "name": "39",
                        "type": "text",
                        "label": "(AN)"
                    },
                    {
                        "name": "40",
                        "type": "text",
                        "label": "(AO)"
                    },
                    {
                        "name": "41",
                        "type": "text",
                        "label": "(AP)"
                    },
                    {
                        "name": "42",
                        "type": "text",
                        "label": "(AQ)"
                    },
                    {
                        "name": "43",
                        "type": "text",
                        "label": "(AR)"
                    },
                    {
                        "name": "44",
                        "type": "text",
                        "label": "(AS)"
                    },
                    {
                        "name": "45",
                        "type": "text",
                        "label": "(AT)"
                    },
                    {
                        "name": "46",
                        "type": "text",
                        "label": "(AU)"
                    },
                    {
                        "name": "47",
                        "type": "text",
                        "label": "(AV)"
                    },
                    {
                        "name": "48",
                        "type": "text",
                        "label": "(AW)"
                    },
                    {
                        "name": "49",
                        "type": "text",
                        "label": "(AX)"
                    },
                    {
                        "name": "50",
                        "type": "text",
                        "label": "(AY)"
                    },
                    {
                        "name": "51",
                        "type": "text",
                        "label": "(AZ)"
                    },
                    {
                        "name": "52",
                        "type": "text",
                        "label": "(BA)"
                    },
                    {
                        "name": "53",
                        "type": "text",
                        "label": "(BB)"
                    },
                    {
                        "name": "54",
                        "type": "text",
                        "label": "(BC)"
                    },
                    {
                        "name": "55",
                        "type": "text",
                        "label": "(BD)"
                    },
                    {
                        "name": "56",
                        "type": "text",
                        "label": "(BE)"
                    },
                    {
                        "name": "57",
                        "type": "text",
                        "label": "(BF)"
                    },
                    {
                        "name": "58",
                        "type": "text",
                        "label": "(BG)"
                    },
                    {
                        "name": "59",
                        "type": "text",
                        "label": "(BH)"
                    },
                    {
                        "name": "60",
                        "type": "text",
                        "label": "(BI)"
                    },
                    {
                        "name": "61",
                        "type": "text",
                        "label": "(BJ)"
                    },
                    {
                        "name": "62",
                        "type": "text",
                        "label": "(BK)"
                    },
                    {
                        "name": "63",
                        "type": "text",
                        "label": "(BL)"
                    },
                    {
                        "name": "64",
                        "type": "text",
                        "label": "(BM)"
                    },
                    {
                        "name": "65",
                        "type": "text",
                        "label": "(BN)"
                    },
                    {
                        "name": "66",
                        "type": "text",
                        "label": "(BO)"
                    },
                    {
                        "name": "67",
                        "type": "text",
                        "label": "(BP)"
                    },
                    {
                        "name": "68",
                        "type": "text",
                        "label": "(BQ)"
                    },
                    {
                        "name": "69",
                        "type": "text",
                        "label": "(BR)"
                    },
                    {
                        "name": "70",
                        "type": "text",
                        "label": "(BS)"
                    },
                    {
                        "name": "71",
                        "type": "text",
                        "label": "(BT)"
                    },
                    {
                        "name": "72",
                        "type": "text",
                        "label": "(BU)"
                    },
                    {
                        "name": "73",
                        "type": "text",
                        "label": "(BV)"
                    },
                    {
                        "name": "74",
                        "type": "text",
                        "label": "(BW)"
                    },
                    {
                        "name": "75",
                        "type": "text",
                        "label": "(BX)"
                    },
                    {
                        "name": "76",
                        "type": "text",
                        "label": "(BY)"
                    },
                    {
                        "name": "77",
                        "type": "text",
                        "label": "(BZ)"
                    },
                    {
                        "name": "78",
                        "type": "text",
                        "label": "(CA)"
                    },
                    {
                        "name": "79",
                        "type": "text",
                        "label": "(CB)"
                    },
                    {
                        "name": "80",
                        "type": "text",
                        "label": "(CC)"
                    },
                    {
                        "name": "81",
                        "type": "text",
                        "label": "(CD)"
                    },
                    {
                        "name": "82",
                        "type": "text",
                        "label": "(CE)"
                    },
                    {
                        "name": "83",
                        "type": "text",
                        "label": "(CF)"
                    },
                    {
                        "name": "84",
                        "type": "text",
                        "label": "(CG)"
                    },
                    {
                        "name": "85",
                        "type": "text",
                        "label": "(CH)"
                    },
                    {
                        "name": "86",
                        "type": "text",
                        "label": "(CI)"
                    },
                    {
                        "name": "87",
                        "type": "text",
                        "label": "(CJ)"
                    },
                    {
                        "name": "88",
                        "type": "text",
                        "label": "(CK)"
                    },
                    {
                        "name": "89",
                        "type": "text",
                        "label": "(CL)"
                    },
                    {
                        "name": "90",
                        "type": "text",
                        "label": "(CM)"
                    },
                    {
                        "name": "91",
                        "type": "text",
                        "label": "(CN)"
                    },
                    {
                        "name": "92",
                        "type": "text",
                        "label": "(CO)"
                    },
                    {
                        "name": "93",
                        "type": "text",
                        "label": "(CP)"
                    },
                    {
                        "name": "94",
                        "type": "text",
                        "label": "(CQ)"
                    },
                    {
                        "name": "95",
                        "type": "text",
                        "label": "(CR)"
                    },
                    {
                        "name": "96",
                        "type": "text",
                        "label": "(CS)"
                    },
                    {
                        "name": "97",
                        "type": "text",
                        "label": "(CT)"
                    },
                    {
                        "name": "98",
                        "type": "text",
                        "label": "(CU)"
                    },
                    {
                        "name": "99",
                        "type": "text",
                        "label": "(CV)"
                    },
                    {
                        "name": "100",
                        "type": "text",
                        "label": "(CW)"
                    },
                    {
                        "name": "101",
                        "type": "text",
                        "label": "(CX)"
                    },
                    {
                        "name": "102",
                        "type": "text",
                        "label": "(CY)"
                    },
                    {
                        "name": "103",
                        "type": "text",
                        "label": "(CZ)"
                    }
                ]
            }
        },
        {
            "id": 3,
            "module": "regexp:Parser",
            "version": 1,
            "parameters": {
                "global": false,
                "pattern": "id=([^&]+)",
                "multiline": false,
                "sensitive": true,
                "singleline": false,
                "continueWhenNoRes": false
            },
            "mapper": {
                "text": "{{2.`3`}}"
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
            "id": 6,
            "module": "google-drive:getAFile",
            "version": 4,
            "parameters": {
                "__IMTCONN__": 3510711
            },
            "mapper": {
                "file": "{{3.`$1`}}",
                "select": "map",
                "formatDrawings": "image/jpeg",
                "formatDocuments": "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
                "formatSpreadsheets": "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
                "formatPresentations": "application/vnd.openxmlformats-officedocument.presentationml.presentation"
            },
            "metadata": {
                "designer": {
                    "x": 600,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "select": {
                            "label": "Enter manually"
                        },
                        "formatDrawings": {
                            "label": "JPEG"
                        },
                        "formatDocuments": {
                            "label": "MS Word Document"
                        },
                        "formatSpreadsheets": {
                            "label": "MS Excel"
                        },
                        "formatPresentations": {
                            "label": "MS PowerPoint"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "google-restricted"
                            },
                            "label": "My Google Restricted connection (gastorellano@gmail.com)"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:google-restricted",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "select",
                        "type": "select",
                        "label": "Enter a File ID",
                        "required": true,
                        "validate": {
                            "enum": [
                                "map",
                                "value"
                            ]
                        }
                    },
                    {
                        "name": "formatDocuments",
                        "type": "select",
                        "label": "Convert Google Documents Files to Format",
                        "required": true,
                        "validate": {
                            "enum": [
                                "application/vnd.openxmlformats-officedocument.wordprocessingml.document",
                                "application/pdf",
                                "application/vnd.oasis.opendocument.text",
                                "text/html",
                                "text/plain",
                                "application/rtf",
                                "text/markdown"
                            ]
                        }
                    },
                    {
                        "name": "formatSpreadsheets",
                        "type": "select",
                        "label": "Convert Google Spreadsheets Files to Format",
                        "required": true,
                        "validate": {
                            "enum": [
                                "application/vnd.openxmlformats-officedocument.spreadsheetml.sheet",
                                "application/x-vnd.oasis.opendocument.spreadsheet",
                                "application/pdf",
                                "text/csv"
                            ]
                        }
                    },
                    {
                        "name": "formatPresentations",
                        "type": "select",
                        "label": "Convert Google Slides Files to Format",
                        "required": true,
                        "validate": {
                            "enum": [
                                "application/vnd.openxmlformats-officedocument.presentationml.presentation",
                                "application/pdf"
                            ]
                        }
                    },
                    {
                        "name": "formatDrawings",
                        "type": "select",
                        "label": "Convert Google Drawings Files to Format",
                        "required": true,
                        "validate": {
                            "enum": [
                                "image/jpeg",
                                "image/png",
                                "image/svg+xml",
                                "application/pdf"
                            ]
                        }
                    },
                    {
                        "name": "file",
                        "type": "text",
                        "label": "File ID",
                        "required": true
                    }
                ],
                "advanced": true
            }
        },
        {
            "id": 11,
            "module": "pdf-co:AnythingToPDF",
            "version": 1,
            "parameters": {
                "__IMTCONN__": 4271375
            },
            "mapper": {
                "async": 15000,
                "export": {
                    "exportType": "inline"
                },
                "import": {
                    "data": "{{6.data}}",
                    "name": "{{6.name}}",
                    "importType": "upload"
                },
                "convert": {
                    "convertType": "fromDocument"
                },
                "expiration": "60"
            },
            "metadata": {
                "designer": {
                    "x": 900,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "async": {
                            "mode": "chose",
                            "label": "Async"
                        },
                        "export": {
                            "nested": {
                                "exportType": {
                                    "label": "JSON Output"
                                }
                            }
                        },
                        "import": {
                            "nested": {
                                "importType": {
                                    "label": "Upload a File"
                                }
                            }
                        },
                        "convert": {
                            "nested": {
                                "convertType": {
                                    "label": "Document to PDF"
                                }
                            }
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "pdf-co"
                            },
                            "label": "My PDF.co connection"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:pdf-co",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "import",
                        "spec": [
                            {
                                "name": "importType",
                                "type": "select",
                                "label": "Input File",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "upload",
                                        "url"
                                    ]
                                }
                            },
                            {
                                "name": "data",
                                "type": "buffer",
                                "label": "Data",
                                "required": true
                            },
                            {
                                "name": "name",
                                "type": "text",
                                "label": "Output File Name"
                            }
                        ],
                        "type": "collection",
                        "label": "Import Options",
                        "required": true
                    },
                    {
                        "name": "convert",
                        "spec": [
                            {
                                "name": "convertType",
                                "type": "select",
                                "label": "Convert Type",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "fromEmail",
                                        "fromImage",
                                        "fromDocument",
                                        "fromSpreadsheet"
                                    ]
                                }
                            }
                        ],
                        "type": "collection",
                        "label": "Convert Options",
                        "required": true
                    },
                    {
                        "name": "async",
                        "type": "select",
                        "label": "Execution Mode",
                        "validate": {
                            "enum": [
                                false,
                                15000,
                                "asyncCheckLater"
                            ]
                        }
                    },
                    {
                        "name": "profiles",
                        "type": "text",
                        "label": "Profiles"
                    },
                    {
                        "name": "expiration",
                        "type": "number",
                        "label": "Output Links Expiration"
                    },
                    {
                        "name": "export",
                        "spec": [
                            {
                                "name": "exportType",
                                "type": "select",
                                "label": "Export Type",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "downloadFile",
                                        "inline"
                                    ]
                                }
                            }
                        ],
                        "type": "collection",
                        "label": "Export options",
                        "required": true
                    }
                ],
                "advanced": true
            }
        },
        {
            "id": 12,
            "module": "pdf-co:PDFToAnything",
            "version": 1,
            "parameters": {
                "__IMTCONN__": 4271375
            },
            "mapper": {
                "async": 15000,
                "export": {
                    "exportType": "inline"
                },
                "import": {
                    "url": "{{11.url}}",
                    "name": "{{11.name}}",
                    "importType": "url"
                },
                "inline": true,
                "convert": {
                    "convertType": "toTextSimple"
                },
                "expiration": "60"
            },
            "metadata": {
                "designer": {
                    "x": 1200,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "async": {
                            "mode": "chose",
                            "label": "Async"
                        },
                        "export": {
                            "nested": {
                                "exportType": {
                                    "label": "JSON Output"
                                }
                            }
                        },
                        "import": {
                            "nested": {
                                "importType": {
                                    "label": "Import a File From URL"
                                }
                            }
                        },
                        "inline": {
                            "mode": "chose"
                        },
                        "convert": {
                            "nested": {
                                "convertType": {
                                    "label": "PDF to Text (simple, no layout, fast)"
                                }
                            }
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "pdf-co"
                            },
                            "label": "My PDF.co connection"
                        }
                    }
                },
                "parameters": [
                    {
                        "name": "__IMTCONN__",
                        "type": "account:pdf-co",
                        "label": "Connection",
                        "required": true
                    }
                ],
                "expect": [
                    {
                        "name": "import",
                        "spec": [
                            {
                                "name": "importType",
                                "type": "select",
                                "label": "Input File",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "upload",
                                        "url"
                                    ]
                                }
                            },
                            {
                                "name": "url",
                                "type": "text",
                                "label": "Url",
                                "required": true
                            },
                            {
                                "name": "name",
                                "type": "text",
                                "label": "Output File Name"
                            }
                        ],
                        "type": "collection",
                        "label": "Import Options",
                        "required": true
                    },
                    {
                        "name": "convert",
                        "spec": [
                            {
                                "name": "convertType",
                                "type": "select",
                                "label": "Convert Type",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "toTextSimple",
                                        "toText",
                                        "toCsv",
                                        "toJson2",
                                        "toJson",
                                        "toXml",
                                        "toHtml",
                                        "toJpg",
                                        "toPng",
                                        "toTiff"
                                    ]
                                }
                            }
                        ],
                        "type": "collection",
                        "label": "Convert Options",
                        "required": true
                    },
                    {
                        "name": "pages",
                        "type": "text",
                        "label": "Pages"
                    },
                    {
                        "name": "password",
                        "type": "text",
                        "label": "Password"
                    },
                    {
                        "name": "inline",
                        "type": "boolean",
                        "label": "Inline"
                    },
                    {
                        "name": "async",
                        "type": "select",
                        "label": "Execution Mode",
                        "validate": {
                            "enum": [
                                false,
                                15000,
                                "asyncCheckLater"
                            ]
                        }
                    },
                    {
                        "name": "profiles",
                        "type": "text",
                        "label": "Profiles"
                    },
                    {
                        "name": "expiration",
                        "type": "number",
                        "label": "Output Links Expiration"
                    },
                    {
                        "name": "export",
                        "spec": [
                            {
                                "name": "exportType",
                                "type": "select",
                                "label": "Export Type",
                                "required": true,
                                "validate": {
                                    "enum": [
                                        "downloadFile",
                                        "inline"
                                    ]
                                }
                            }
                        ],
                        "type": "collection",
                        "label": "Export Options",
                        "required": true
                    }
                ],
                "advanced": true
            }
        },
        {
            "id": 15,
            "module": "gemini-ai:createACompletionGeminiPro",
            "version": 1,
            "parameters": {
                "__IMTCONN__": 4273184
            },
            "mapper": {
                "model": "gemini-2.0-flash",
                "contents": [
                    {
                        "role": "model",
                        "parts": [
                            {
                                "text": "Eres un asistente de IA especializado en el análisis de currículums vitae en español. Tu tarea es leer el contenido textual del archivo adjunto{{12.body.objects_value}} y devolver un resumen profesional útil para ser utilizado en una base de datos de candidatos.\r\n\r\nLos campos a extraer son:\r\n1. `Resumen`: Una lista breve (de una a tres palabras clave) que resuma el perfil profesional del candidato. Por ejemplo: \"Abogado\", \"Analista de datos\", \"Data Science\", \"Desarrollador Python\", etc.\r\n2. `Observaciones`: Una descripción concisa del perfil profesional de la persona. Debe ser en tercera persona, no mayor a dos líneas. Ejemplo: \"Abogado con orientación tecnológica y formación en análisis de datos\".\r\n\r\nInstrucciones importantes:\r\n- Asegúrate de basarte exclusivamente en el contenido del archivo. Si no hay información suficiente, devuelve `null` en el campo correspondiente.\r\n- La salida debe ser **EXCLUSIVAMENTE** un objeto JSON, sin introducciones, sin explicaciones adicionales, y sin bloques de código como ```json.\r\n- El JSON debe contener **solo las claves `Resumen` y `Observaciones`**.\r\n\r\nEjemplo del formato de salida esperado:\r\n{\r\n  \"Resumen\": \"Abogado, Data Analyst\",\r\n  \"Observaciones\": \"Abogado con formación técnica y orientación al análisis de datos aplicados al sector legal.\"\r\n}\r\n",
                                "type": "text"
                            }
                        ]
                    },
                    {
                        "role": "user",
                        "parts": [
                            {
                                "text": "Por favor, analizá el archivo adjunto{{12.body.objects_value}} que contiene un currículum vitae. Extraé del contenido dos elementos específicos:\r\n\r\n1. Un resumen breve del perfil profesional, en forma de lista de palabras clave (Ej: Abogado, Data Analyst, etc.).\r\n2. Una observación concisa en tercera persona que describa el perfil.\r\n\r\nDevolvé el resultado en formato **estrictamente JSON** con las claves:\r\n\r\n- `Resumen`\r\n- `Observaciones`\r\n\r\nArchivo para analizar:\r\nMessage type: File  \r\nMime type: 12. Mime Type (del módulo de carga anterior)  \r\nFile URI: 12. URI (del módulo de carga anterior)\r\n",
                                "type": "text"
                            }
                        ]
                    }
                ],
                "generationConfig": {
                    "thinkingConfig": {}
                }
            },
            "metadata": {
                "designer": {
                    "x": 1500,
                    "y": 0
                },
                "restore": {
                    "expect": {
                        "model": {
                            "mode": "chose",
                            "label": "Gemini 2.0 Flash"
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
                                "responseModalities": {
                                    "mode": "chose"
                                }
                            }
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
                                "type": "text",
                                "label": "Response Mime Type"
                            },
                            {
                                "name": "responseSchema",
                                "type": "any",
                                "label": "Response Schema"
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
                    }
                ]
            }
        },
        {
            "id": 17,
            "module": "regexp:Parser",
            "version": 1,
            "parameters": {
                "global": false,
                "pattern": "json\\s*(\\{[\\s\\S]*?\\})\\s*",
                "multiline": true,
                "sensitive": true,
                "singleline": false,
                "continueWhenNoRes": true
            },
            "mapper": {
                "text": "{{15.result}}"
            },
            "metadata": {
                "designer": {
                    "x": 1800,
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
            "id": 18,
            "module": "json:ParseJSON",
            "version": 1,
            "parameters": {
                "type": 140613
            },
            "mapper": {
                "json": "{{17.`$1`}}"
            },
            "metadata": {
                "designer": {
                    "x": 2100,
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
                        "name": "Resumen",
                        "type": "text",
                        "label": "Resumen",
                        "default": null,
                        "required": false,
                        "multiline": false
                    },
                    {
                        "help": "",
                        "name": "Observaciones",
                        "type": "text",
                        "label": "Observaciones",
                        "default": null,
                        "required": false,
                        "multiline": false
                    }
                ]
            }
        },
        {
            "id": 19,
            "module": "google-sheets:updateRow",
            "version": 2,
            "parameters": {
                "__IMTCONN__": 3522692
            },
            "mapper": {
                "from": "drive",
                "mode": "select",
                "values": {
                    "9": "{{18.Resumen}}",
                    "10": "{{18.Observaciones}}"
                },
                "sheetId": "Respuestas de formulario 1",
                "rowNumber": "{{2.`__ROW_NUMBER__`}}",
                "spreadsheetId": "/1OFPZYYtaWyaXsBxMpy78o38H-wEPEaOj/1Tn6o8HcAf4exkvFE7ZcOrh1MIU4xYaTd/19NFO08IQG0lFvfqp4fg_IdD_GhnuzA_S0xZUEo1o4bo",
                "includesHeaders": true,
                "valueInputOption": "USER_ENTERED"
            },
            "metadata": {
                "designer": {
                    "x": 2400,
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
                            "label": "Respuestas de formulario 1"
                        },
                        "spreadsheetId": {
                            "path": [
                                "UPLIN",
                                "BOLSA DE EMPLEO",
                                "CARGA DE CV (Respuestas)"
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
                                            "label": "Marca temporal (A)"
                                        },
                                        {
                                            "name": "1",
                                            "type": "text",
                                            "label": "Dirección de correo electrónico (B)"
                                        },
                                        {
                                            "name": "2",
                                            "type": "text",
                                            "label": "Apellido y Nombre (C)"
                                        },
                                        {
                                            "name": "3",
                                            "type": "text",
                                            "label": "Carga tu hoja de vida o CV (PDF o Word) (D)"
                                        },
                                        {
                                            "name": "4",
                                            "type": "text",
                                            "label": "Correo electrónico (E)"
                                        },
                                        {
                                            "name": "5",
                                            "type": "text",
                                            "label": "Localidad, Provincia/Estado, País (Código Postal) (F)"
                                        },
                                        {
                                            "name": "6",
                                            "type": "text",
                                            "label": "Número de teléfono (G)"
                                        },
                                        {
                                            "name": "7",
                                            "type": "text",
                                            "label": "Comentarios adicionales (H)"
                                        },
                                        {
                                            "name": "8",
                                            "type": "text",
                                            "label": "Privacidad de los datos (I)"
                                        },
                                        {
                                            "name": "9",
                                            "type": "text",
                                            "label": "Resumen (J)"
                                        },
                                        {
                                            "name": "10",
                                            "type": "text",
                                            "label": "Observaciones (K)"
                                        },
                                        {
                                            "name": "11",
                                            "type": "text",
                                            "label": "Correo de Bienvenida (L)"
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
                                        }
                                    ],
                                    "type": "collection",
                                    "label": "Values"
                                }
                            ]
                        },
                        "valueInputOption": {
                            "mode": "chose",
                            "label": "User entered"
                        }
                    },
                    "parameters": {
                        "__IMTCONN__": {
                            "data": {
                                "scoped": "true",
                                "connection": "google"
                            },
                            "label": "My Google connection (gastorellano@gmail.com)"
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
                        "name": "rowNumber",
                        "type": "uinteger",
                        "label": "Row number",
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
                                "label": "Marca temporal (A)"
                            },
                            {
                                "name": "1",
                                "type": "text",
                                "label": "Dirección de correo electrónico (B)"
                            },
                            {
                                "name": "2",
                                "type": "text",
                                "label": "Apellido y Nombre (C)"
                            },
                            {
                                "name": "3",
                                "type": "text",
                                "label": "Carga tu hoja de vida o CV (PDF o Word) (D)"
                            },
                            {
                                "name": "4",
                                "type": "text",
                                "label": "Correo electrónico (E)"
                            },
                            {
                                "name": "5",
                                "type": "text",
                                "label": "Localidad, Provincia/Estado, País (Código Postal) (F)"
                            },
                            {
                                "name": "6",
                                "type": "text",
                                "label": "Número de teléfono (G)"
                            },
                            {
                                "name": "7",
                                "type": "text",
                                "label": "Comentarios adicionales (H)"
                            },
                            {
                                "name": "8",
                                "type": "text",
                                "label": "Privacidad de los datos (I)"
                            },
                            {
                                "name": "9",
                                "type": "text",
                                "label": "Resumen (J)"
                            },
                            {
                                "name": "10",
                                "type": "text",
                                "label": "Observaciones (K)"
                            },
                            {
                                "name": "11",
                                "type": "text",
                                "label": "Correo de Bienvenida (L)"
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
                            }
                        ],
                        "type": "collection",
                        "label": "Values"
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

Estos flujos permitieron:  
- **Centralizar leads** de webinars, charlas y clientes en bases de datos únicas.  
- **Reducir errores humanos**, al automatizar la extracción y parseo de información.  
- **Acelerar procesos internos**, como la revisión de CVs o la actualización de tablas administrativas.  
- **Mejorar la trazabilidad**, generando registros consistentes para marketing, ventas y RRHH.  

---
