# Patching

Instructor enhances client functionality with three new keywords for backwards compatibility. This allows use of the enhanced client as usual, with structured output benefits.

- `response_model`: Defines the response type for `chat.completions.create`.
- `max_retries`: Determines retry attempts for failed `chat.completions.create` validations.
- `validation_context`: Provides extra context to the validation process.

The default mode is `instructor.Mode.TOOLS` which is the recommended mode for OpenAI clients. This mode is the most stable and is the most recommended for OpenAI clients. The other modes are for other clients and are not recommended for OpenAI clients.

## Tool Calling

This is the recommended method for OpenAI clients. It is the most stable as functions is being deprecated soon.

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.TOOLS)
```

## Parallel Tool Calling

Parallel tool calling is also an option but you must set `response_model` to be `Iterable[Union[...]]` types since we expect an array of results. Check out [Parallel Tool Calling](./parallel.md) for more information.

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.PARALLEL_TOOLS)
```

## Function Calling

Note that function calling is soon to be deprecated in favor of TOOL mode for OpenAI. But will still be supported for other clients.

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.FUNCTIONS)
```

## JSON Mode

JSON mode uses OpenAI's JSON fromat for responses. by setting `response_format={"type": "json_object"}` in the `chat.completions.create` method.

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.JSON)
```

## JSON Schema Mode

JSON Schema mode uses OpenAI's JSON fromat for responses. by setting `response_format={"type": "json_object", schema:response_model.model_json_schema()}` in the `chat.completions.create` method. This is only available for select clients (e.g. llama-cpp-python, Anyscale, Together)

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.JSON_SCHEMA)
```

## Markdown JSON Mode

This just asks for the response in JSON format, but it is not recommended, and may not be supported in the future, this is just left to support vision models and will not give you the full benefits of instructor.

!!! warning "Experimental"

    This is not recommended, and may not be supported in the future, this is just left to support vision models.

```python
import instructor
from openai import OpenAI

client = instructor.patch(OpenAI(), mode=instructor.Mode.MD_JSON)
```
