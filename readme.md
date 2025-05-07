# API Endpoint Documentation

## Endpoint: Create Planning Orders

### Description
Creates a planning order by processing a list of orders and planning configuration parameters to optimize production. The endpoint returns direct orders, valid solution combinations, and any orders that could not be paired into solutions.

### URL
```
POST /api/planning-orders
```

### Method
`POST`

### Request Body
- **Content-Type**: `application/json`
- **Structure**:
  ```json
  {
      "orders": [
          {
              "id": integer,
              "client": string,
              "category": string,
              "quantity": integer,
              "width": number,
              "length": number,
              "high_priority": boolean,
              "finished": string,
              "starch": string,
              "ribbing_type": string,
              "created_at": string,
              "updated_at": string,
              "materials": array
          },
          ...
      ],
      "planning_configuration": {
          "static-trim": number,
          "max-solution-order-quantity": integer,
          "min-waste-percentage": number,
          "reel-sizes": [integer, ...],
          "maxItemPerSheet": number
      }
  }
  ```

#### Orders Fields
- `id`: Unique identifier for the order (integer).
- `client`: Name of the client (string).
- `category`: Category of the order (string).
- `quantity`: Number of units required (integer).
- `width`: Width of the order in centimeters (number).
- `length`: Length of the order in centimeters (number).
- `high_priority`: Indicates if the order is high priority (boolean).
- `finished`: Indicates if the order is completed (string, "true" or "false").
- `starch`: Type of starch used (string, e.g., "processed").
- `ribbing_type`: Type of ribbing (string, e.g., "c").
- `created_at`: Timestamp of order creation (ISO 8601 string).
- `updated_at`: Timestamp of last order update (ISO 8601 string).
- `materials`: Array of material specifications (structure varies).

#### Planning Configuration Fields
- `static-trim`: Fixed trim size in centimeters (float, e.g., 3.0).
- `max-solution-order-quantity`: Maximum quantity per solution (integer, e.g., 25000).
- `min-waste-percentage`: Minimum acceptable waste percentage (float, e.g., 0.03).
- `reel-sizes`: List of available reel sizes in centimeters (array of integers, e.g., [180, 185, 190, 195, 200, 205, 210, 215, 220]).
- `maxItemPerSheet`: Maximum number of items per sheet (float, e.g., 6.0).

### Authentication
No authentication required for this endpoint.

### Response
#### Success Response
- **Status Code**: `201 Created`
- **Content-Type**: `application/json`
- **Body**:
  ```json
  {
      "directListOrders": [
          {
              "id": integer,
              "width": number,
              "length": number,
              "quantity": integer,
              "materials": array,
              "ribbing_type": string,
              "waste_by_cm": array,
              "min_waste_by_cm": number,
              "waste_by_cm_percentage_list": array,
              "waste_by_cm_percentage": number,
              "weight_waste_in_layer_meter_square": array,
              "weight_waste_in_layer1_meter_square": array,
              "weight_waste_in_layer2_meter_square": array,
              "weight_waste_in_layer3_meter_square": array,
              "weight_waste_in_layer4_meter_square": array,
              "weight_waste_in_layer5_meter_square": array,
              "weight_waste_width_in_order_meter_square": array,
              "total_waste_for_all_materials_by_meter_square": array,
              "total_waste_for_all_materials_by_ton": array,
              "itemPerSheets": array,
              "bestItemPerSheets": number,
              "cuts": array,
              "bestCuts": number,
              "best_size": integer,
              "size_used": number,
              "additionalWaste": number,
              "sheet_area_seq_by_m": number,
              "order_area_seq_by_m": number,
              "metric_length_of_form": number,
              "best_length_m": number,
              "best_waste_m": number,
              "best_waste_ton": number,
              "best_waste_kg": number,
              "solution_weight_ton": number,
              "solution_weight_kg": number,
              "total_waste": number
          },
          ...
      ],
      "validSolutions": [
          {
              "order1_id": integer,
              "order1_width": number,
              "order1_length": number,
              "order1_quantity": number,
              "order1_cut": number,
              "order1_net": number,
              "order1_remaining": number,
              "order1_materials": array,
              "order1_starch": string,
              "order1_ribbing_type": string,
              "produced_quantity_1": integer,
              "order2_id": integer,
              "order2_width": number,
              "order2_length": number,
              "order2_quantity": number,
              "order2_cut": number,
              "order2_net": number,
              "order2_remaining": number,
              "order2_materials": array,
              "order2_starch": string,
              "order2_ribbing_type": string,
              "produced_quantity_2": integer,
              "additionalWaste": number,
              "size_used": integer,
              "best_size": number,
              "waste_by_cm": number,
              "waste_percentage": number,
              "waste_in_ton": number,
              "best_waste_kg": number,
              "metric_length_of_form": number,
              "solution_weight_ton": number,
              "solution_weight_kg": number,
              "total_waste": number
          },
          ...
      ],
      "ordersNotHaveSolutions": [
          {
              "id": integer,
              "width": number,
              "length": number,
              "quantity": integer,
              "materials": array,
              "ribbing_type": string,
              "waste_by_cm": array,
              "min_waste_by_cm": number,
              "waste_by_cm_percentage_list": array,
              "waste_by_cm_percentage": number,
              "weight_waste_in_layer_meter_square": array,
              "weight_waste_in_layer1_meter_square": array,
              "weight_waste_in_layer2_meter_square": array,
              "weight_waste_in_layer3_meter_square": array,
              "weight_waste_in_layer4_meter_square": array,
              "weight_waste_in_layer5_meter_square": array,
              "weight_waste_width_in_order_meter_square": array,
              "total_waste_for_all_materials_by_meter_square": array,
              "total_waste_for_all_materials_by_ton": array,
              "itemPerSheets": array,
              "bestItemPerSheets": number,
              "cuts": array,
              "bestCuts": number,
              "best_size": integer,
              "size_used": number,
              "additionalWaste": number,
              "sheet_area_seq_by_m": number,
              "order_area_seq_by_m": number,
              "metric_length_of_form": number,
              "best_length_m": number,
              "best_waste_m": number,
              "best_waste_ton": number,
              "best_waste_kg": number,
              "solution_weight_ton": number,
              "solution_weight_kg": number,
              "total_waste": number
          },
          ...
      ]
  }
  ```

#### Response Fields
- **directListOrders**: Orders that can be processed directly without combining with other orders.
  - Includes detailed waste calculations, sheet usage, and solution metrics.
- **validSolutions**: Pairs of orders combined to optimize production.
  - Each solution includes details for two orders, their cuts, remaining quantities, and waste metrics.
- **ordersNotHaveSolutions**: Orders that could not be paired into valid solutions.
  - Structure is identical to `directListOrders` when populated; may be empty.

### Error Responses
- **Status Code**: `400 Bad Request`
  - **Body**:
    ```json
    {
        "error": "Invalid request body",
        "details": ["Missing orders array", "Invalid static-trim value", ...]
    }
    ```
- **Status Code**: `500 Internal Server Error`
  - **Body**:
    ```json
    {
        "error": "Internal server error"
    }
    ```

### Example Request
```json
POST /api/planning-orders
Content-Type: application/json

{
    "orders": [
        {
            "id": 10,
            "client": "Dr. Cole Moen I",
            "category": "veritatis",
            "quantity": 440000,
            "width": 28.8,
            "length": 94.6,
            "high_priority": true,
            "finished": "false",
            "starch": "processed",
            "ribbing_type": "c",
            "created_at": "2025-05-07T10:59:47.000000Z",
            "updated_at": "2025-05-07T10:59:47.000000Z",
            "materials": []
        },
        ...
    ],
    "planning_configuration": {
        "static-trim": 3.0,
        "max-solution-order-quantity": 25000,
        "min-waste-percentage": 0.03,
        "reel-sizes": [180, 185, 190, 195, 200, 205, 210, 215, 220],
        "maxItemPerSheet": 6.0
    }
}
```

### Example Response
```json
{
    "directListOrders": [
        {
            "id": 14,
            "width": 35.3,
            "length": 94.6,
            "quantity": 337500,
            "materials": [],
            "ribbing_type": "c",
            "waste_by_cm": [],
            "min_waste_by_cm": 0.5,
            "waste_by_cm_percentage_list": [],
            "waste_by_cm_percentage": 1.9,
            "weight_waste_in_layer_meter_square": [],
            "weight_waste_in_layer1_meter_square": [],
            "weight_waste_in_layer2_meter_square": [],
            "weight_waste_in_layer3_meter_square": [],
            "weight_waste_in_layer4_meter_square": [],
            "weight_waste_in_layer5_meter_square": [],
            "weight_waste_width_in_order_meter_square": [],
            "total_waste_for_all_materials_by_meter_square": [],
            "total_waste_for_all_materials_by_ton": [],
            "itemPerSheets": [],
            "bestItemPerSheets": 5.0,
            "cuts": [],
            "bestCuts": 67500.0,
            "best_size": 177,
            "size_used": 180.0,
            "additionalWaste": 3.0,
            "sheet_area_seq_by_m": 0.333938,
            "order_area_seq_by_m": 112704.075,
            "metric_length_of_form": 63855.0,
            "best_length_m": 0.946,
            "best_waste_m": 0.035,
            "best_waste_ton": 0.955430438,
            "best_waste_kg": 955.43,
            "solution_weight_ton": 48.317,
            "solution_weight_kg": 48317.48,
            "total_waste": 3.5
        }
    ],
    "validSolutions": [
        {
            "order1_id": 10,
            "order1_width": 28.8,
            "order1_length": 94.6,
            "order1_quantity": 440000,
            "order1_cut": 6250.0,
            "order1_net": 4.0,
            "order1_remaining": 415000.0,
            "order1_materials": [],
            "order1_starch": "processed",
            "order1_ribbing_type": "c",
            "produced_quantity_1": 25000,
            "order2_id": 11,
            "order2_width": 30.8,
            "order2_length": 94.6,
            "order2_quantity": 450000,
            "order2_cut": 12500.0,
            "order2_net": 2.0,
            "order2_remaining": 437500.0,
            "order2_materials": [],
            "order2_starch": "processed",
            "order2_ribbing_type": "c",
            "produced_quantity_2": 12500,
            "additionalWaste": 3,
            "size_used": 180,
            "best_size": 176.8,
            "waste_by_cm": 0.19999999999999,
            "waste_percentage": 0.001,
            "waste_in_ton": 0.080883,
            "best_waste_kg": 80.88,
            "metric_length_of_form": 5912.5,
            "solution_weight_ton": 4.469,
            "solution_weight_kg": 4468.786,
            "total_waste": 3.2
        },
        ...
    ],
    "ordersNotHaveSolutions": []
}
```

### Notes
- The `directListOrders` and `ordersNotHaveSolutions` arrays share the same structure, containing detailed metrics for waste, cuts, and sheet usage.
- The `validSolutions` array contains paired orders with metrics for each order and combined waste calculations.
- The `ordersNotHaveSolutions` array may be empty or populated with orders that could not be paired, structured identically to `directListOrders`.
- All measurements (width, length, waste_by_cm, etc.) are in centimeters unless specified.
- Waste and solution weights are provided in both tons and kilograms for precision.
- The `materials` array structure is not specified in the input but should be included as provided.
- The endpoint validates that all required fields are present and correctly formatted before processing.
