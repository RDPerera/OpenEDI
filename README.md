## **OpenEDI Schema Specification**

### **Introduction**
OpenEDI is an open-source schema definition and conversion framework designed to standardize the way EDI files are defined, parsed, and converted into JSON or other REST-friendly formats. It supports multiple EDI standards (X12, EDIFACT, HL7, etc.) and allows users to define custom schemas for specific EDI formats. OpenEDI schemas are JSON-based, making them easy to read, write, and share.

The schema specification defines the structure of EDI files, including segments, fields, components, and subcomponents, along with advanced features like validation rules, transformations, and extensibility.

---

### **Key Features of OpenEDI**
1. **Multi-Standard Support**: Define schemas for X12, EDIFACT, HL7, and other EDI formats.
2. **Custom Schema Definitions**: Create custom schemas for specific EDI documents (e.g., X12 850, EDIFACT ORDERS).
3. **Validation Rules**: Define constraints for fields, components, and subcomponents (e.g., data types, lengths, required fields).
4. **Extensibility**: Add custom metadata, transformations, and extensions to schemas.
5. **REST-Friendly Output**: Convert EDI files into JSON or other REST-friendly formats.
6. **Open Source**: Fully open-source and community-driven.

---

### **Understanding the Structure of EDI**
EDI files are hierarchical and consist of:
- **Segments**: Logical groupings of related data (e.g., header, item details).
- **Fields**: Individual data elements within a segment.
- **Components**: Sub-elements within a field (used in composite fields).
- **Subcomponents**: Further subdivisions of components.

Example:
```
EDI File
├── Segment1 (Header)
│   ├── Field1 (Code)
│   ├── Field2 (OrderId)
│   ├── Field3 (Organization)
│   └── Field4 (Date)
│       ├── Component1 (Year)
│       └── Component2 (Month)
├── Segment2 (Items)
│   ├── Field1 (Code)
│   ├── Field2 (Item)
│   └── Field3 (Quantity)
│       └── Component1 (Measurement)
│           └── Sub-Component1 (Unit)
└── ...
```

---

### **OpenEDI Schema Specification**

#### **1. Schema Metadata**
- **name**: Name of the schema (e.g., "X12_850").
- **description**: A brief description of the schema.
- **ediStandard**: The EDI standard this schema applies to (e.g., "X12", "EDIFACT", "HL7").
- **version**: Schema version (e.g., "1.0.0").

Example:
```json
{
  "name": "X12_850",
  "description": "Schema for X12 850 Purchase Order",
  "ediStandard": "X12",
  "version": "1.0.0"
}
```

---

#### **2. Delimiters**
Define the characters used to separate segments, fields, components, and subcomponents.

- **segment**: Segment delimiter (default: `~`).
- **field**: Field delimiter (default: `*`).
- **component**: Component delimiter (default: `:`).
- **subcomponent**: Subcomponent delimiter (default: `^`).
- **repetition**: Repetition delimiter (default: `>`).

Example:
```json
"delimiters": {
  "segment": "~",
  "field": "*",
  "component": ":",
  "subcomponent": "^",
  "repetition": ">"
}
```

---

#### **3. Segments**
Define the segments in the EDI file.

- **code**: The segment code (e.g., "HDR").
- **tag**: A user-friendly name for the segment (e.g., "header").
- **minOccurrences**: Minimum number of occurrences (default: 1).
- **maxOccurrences**: Maximum number of occurrences (`-1` for unlimited).
- **fields**: Array of fields in the segment.

Example:
```json
"segments": [
  {
    "code": "HDR",
    "tag": "header",
    "minOccurrences": 1,
    "maxOccurrences": 1,
    "fields": [
      {"tag": "code", "required": true},
      {"tag": "orderId", "required": true},
      {"tag": "organization"},
      {"tag": "date"}
    ]
  },
  {
    "code": "ITM",
    "tag": "items",
    "minOccurrences": 0,
    "maxOccurrences": -1,
    "fields": [
      {"tag": "code", "required": true},
      {"tag": "item", "required": true},
      {"tag": "quantity", "dataType": "int"}
    ]
  }
]
```

---

#### **4. Fields**
Define the fields within a segment.

- **tag**: A user-friendly name for the field.
- **required**: Whether the field is mandatory (default: `false`).
- **dataType**: Data type of the field (e.g., "string", "int", "float", "composite").
- **length**: Length constraints (fixed, min, max).
- **components**: Array of components (for composite fields).

Example:
```json
"fields": [
  {
    "tag": "CustomerName",
    "dataType": "string",
    "length": {"min": 1, "max": 50}
  },
  {
    "tag": "Quantity",
    "dataType": "int",
    "length": {"min": 1}
  },
  {
    "tag": "Address",
    "dataType": "composite",
    "components": [
      {"tag": "Street", "dataType": "string"},
      {"tag": "City", "dataType": "string"},
      {"tag": "ZipCode", "dataType": "string"}
    ]
  }
]
```

---

#### **5. Components and Subcomponents**
Define components and subcomponents for composite fields.

- **tag**: A user-friendly name for the component/subcomponent.
- **required**: Whether the component/subcomponent is mandatory.
- **dataType**: Data type of the component/subcomponent.

Example:
```json
"components": [
  {
    "tag": "Street",
    "dataType": "string",
    "required": true
  },
  {
    "tag": "City",
    "dataType": "string",
    "required": true
  },
  {
    "tag": "ZipCode",
    "dataType": "string",
    "length": {"min": 5, "max": 10}
  }
]
```

---

#### **6. Validation Rules**
Define custom validation rules for fields, components, and subcomponents.

- **regex**: Regular expression for validating field values.
- **enum**: List of allowed values.
- **customRule**: Custom validation logic (e.g., "date must be in YYYYMMDD format").

Example:
```json
"validation": {
  "date": {
    "regex": "^\\d{8}$",
    "customRule": "Date must be in YYYYMMDD format"
  },
  "countryCode": {
    "enum": ["US", "CA", "MX"]
  }
}
```

---

#### **7. Transformations**
Define transformations to convert EDI data into JSON or other formats.

- **mapping**: Map EDI fields to JSON keys.
- **defaults**: Default values for missing fields.
- **formatting**: Formatting rules (e.g., date formats).

Example:
```json
"transformations": {
  "mapping": {
    "orderId": "order_id",
    "organization": "org_name"
  },
  "defaults": {
    "quantity": 1
  },
  "formatting": {
    "date": "YYYY-MM-DD"
  }
}
```

---

#### **8. Extensions**
Add custom metadata or extensions to the schema.

- **metadata**: Custom key-value pairs.
- **plugins**: Reference to external plugins or libraries.

Example:
```json
"extensions": {
  "metadata": {
    "author": "OpenEDI Community",
    "license": "Apache 2.0"
  },
  "plugins": ["openedi-x12-plugin"]
}
```

---

### **Example OpenEDI Schema**
```json
{
  "name": "X12_850",
  "description": "Schema for X12 850 Purchase Order",
  "ediStandard": "X12",
  "version": "1.0.0",
  "delimiters": {
    "segment": "~",
    "field": "*",
    "component": ":",
    "subcomponent": "^",
    "repetition": ">"
  },
  "segments": [
    {
      "code": "HDR",
      "tag": "header",
      "minOccurrences": 1,
      "maxOccurrences": 1,
      "fields": [
        {"tag": "code", "required": true},
        {"tag": "orderId", "required": true},
        {"tag": "organization"},
        {"tag": "date"}
      ]
    }
  ],
  "transformations": {
    "mapping": {
      "orderId": "order_id",
      "organization": "org_name"
    }
  }
}
```
