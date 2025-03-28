## Integration of a Mathematical Calulations with a Chat Completion System using LLM Function-Calling

### AIM:
To design and implement a Python function for calculating the volume of a cylinder, integrate it with a chat completion system utilizing the function-calling feature of a large language model (LLM).

### PROBLEM STATEMENT:

### DESIGN STEPS:

#### STEP 1: DESIGN THE VOLUME CALCULATION FUNCTION
1.Create a function calculate_cylinder_volume(radius, height) to compute the volume using the formula (v=3.14 * radius * radius * height)\
2.Validate that both radius and height are positive numbers.

#### STEP 2: BUILD THE CHAT SYSTEM
1.Interact with the user, detect volume calculation requests, and prompt for radius and height.\
2.Call calculate_volume_cylinder with user input and display the result.

#### STEP 3: INTEGRATE WITH LLM FUNCTION-CALLING
1.Define chat_with_openai() to pass user queries to the LLM. \
2.Use the LLM’s function-calling feature to extract radius and height, and call  calculate_volume_cylinder.

### PROGRAM:
```py

import math
import os
import openai
openai.api_key = os.environ['OPENAI_API_KEY']


def calculate_volume_cylinder(radius,height):
    if radius < 1 or height <1:
        return "enter proper values"
    vol = 3.14 * radius * radius * height
    return round(vol,2)
```
```py
messages=[
            {"role": "system", "content": "You are an assistant that helps calculate the volume of a cylinder."},
            {"role": "user", "content": prompt},
        ]
```
```py
functions = [
            {
                "name": "calculate_volume_cylinder",
                "description": "To find the volume of a cylinder",
                "parameters": {
                    "type": "object",
                    "properties": {
                        "radius": {"type": "number", "description": "The radius of the cylinder"},
                        "height": {"type": "number", "description": "The height of the cylinder"}
                    },
                    "required": ["radius", "height"]
                }
            }
        ]
```
```py
def chat_with_openai(prompt):
    response = openai.ChatCompletion.create(
        model="gpt-4",
        messages=messages
        functions = functions,
        function_call = "auto"
    )
    if "function_call" in response["choices"][0]["message"]:
        function_name = response["choices"][0]["message"]["function_call"]["name"]
        arguments = eval(response["choices"][0]["message"]["function_call"]["arguments"])
        if function_name == "calculate_volume_cylinder":
            radius = arguments["radius"]
            height = arguments["height"]
            return calculate_volume_cylinder(radius, height)
                                             
    return response["choices"][0]["message"]["content"]
```
```py
    
radius = float(input("Enter the radius of the cylinder: "))
height = float(input("Enter the height of the cylinder: "))

prompt = f"What is the volume of a cylinder with a radius of {radius} and a height of {height}?"
result = chat_with_openai(prompt)
print("Result:", result)
```


### OUTPUT:
![image](./Screen%20Shot%201947-01-07%20at%2020.41.46.png)

### RESULT:
The system successfully calculates the cylinder volume based on user input and handles errors for invalid values. It integrates with an LLM to extract and process radius and height from user queries. Overall, it provides an interactive and efficient way to compute cylinder volumes with robust error handling.
