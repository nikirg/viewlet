# Renderable

Renderable is a lightweight Python library designed for building modern web interfaces using a component-based approach. It enables writing page logic on the server side in Python, integrating seamlessly with FastAPI. With Renderable, interactive elements like inputs, buttons, and selects trigger component reloads that occur on the server, updating the component's state dynamically.

## Key Features

1. **Component-Based Approach**: Build web interfaces using components that encapsulate logic, state, and presentation.
2. **Server-Side Logic**: Handle interactions and state management on the server, reducing client-side complexity.
3. **FastAPI Integration**: Each component or page is a FastAPI endpoint, allowing for dependency injection and other FastAPI features.
4. **Lightweight**: The only dependencies are FastAPI for Python and HTMX for JavaScript, which can be included via CDN.
5. **State Management**: Utilize a state manager that can trigger component reloads, ensuring a reactive user experience.

## Installation

To install Renderable, use pip:

```bash
pip install renderable
```

## Quick Start

Here's an example application to demonstrate how Renderable works:

```python
from fastapi import Depends, FastAPI
from renderable import RenderableRouter, State as BaseState, tags
from renderable.component import Component


class State(BaseState):
    tasks: list[str] = []


router = RenderableRouter(state_schema=State)


@router.component()
class TodoList(Component):
    async def view(self, state: State = Depends(State.load)):
        with tags.form():
            with tags.div(class_="box"):
                with tags.div(class_="field"):
                    tags.label("Task", class_="label", for_="task")
                    inp = tags.input(
                        class_="input", id="task", name="task", type_="text"
                    )

                with tags.div(class_="field"):
                    submit_btn = tags.button(
                        "Add", id="submit", class_="button", type_="button"
                    )

                    if submit_btn.trigger:
                        async with state:
                            state.tasks.append(inp.value)

                        inp.value = None

        for index, task in enumerate(state.tasks):
            with tags.div(class_="box is-flex is-justify-content-space-between"):
                tags.h2(f"{index + 1}. {task}")

                del_btn = tags.button("x", id=f"del_{index}", class_="button")

                if del_btn.trigger:
                    async with state:
                        state.tasks.pop(index)
                        await self.reload()


def extra_head():
    tags.title("Todo List")
    tags.link(
        rel="stylesheet",
        href="https://cdn.jsdelivr.net/npm/bulma@1.0.0/css/bulma.min.css",
    )


@router.page("/", head=extra_head)
def root():
    with tags.div(class_="container mt-6"):
        TodoList()


app = FastAPI()
app.include_router(router)
```


## Documentation

For more detailed documentation and examples, please visit the official documentation site.

## Contributing

We welcome contributions! Please see our contributing guidelines for more information.


## License

Renderable is licensed under the MIT License.