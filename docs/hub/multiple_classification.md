If you want to try outs via `instructor hub`, you can pull it by running

```bash
instructor hub pull --slug multiple_classification --py > multiple_classification.py
```

For multi-label classification, we introduce a new enum class and a different Pydantic model to handle multiple labels.

```python
import openai
import instructor

from typing import List, Literal
from pydantic import BaseModel, Field

# Apply the patch to the OpenAI client
# enables response_model keyword
client = instructor.patch(openai.OpenAI())

LABELS = Literal["ACCOUNT", "BILLING", "GENERAL_QUERY"]


class MultiClassPrediction(BaseModel):
    labels: List[LABELS] = Field(
        ...,
        description="Only select the labels that apply to the support ticket.",
    )


def multi_classify(data: str) -> MultiClassPrediction:
    return client.chat.completions.create(
        model="gpt-4-turbo-preview",  # gpt-3.5-turbo fails
        response_model=MultiClassPrediction,
        messages=[
            {
                "role": "system",
                "content": f"You are a support agent at a tech company. Only select the labels that apply to the support ticket.",
            },
            {
                "role": "user",
                "content": f"Classify the following support ticket: {data}",
            },
        ],
    )  # type: ignore


if __name__ == "__main__":
    ticket = "My account is locked and I can't access my billing info."
    prediction = multi_classify(ticket)
    assert {"ACCOUNT", "BILLING"} == {label for label in prediction.labels}
    print("input:", ticket)
    #> input: My account is locked and I can't access my billing info.
    print("labels:", LABELS)
    #> labels: typing.Literal['ACCOUNT', 'BILLING', 'GENERAL_QUERY']
    print("prediction:", prediction)
    #> prediction: labels=['ACCOUNT', 'BILLING']
```
