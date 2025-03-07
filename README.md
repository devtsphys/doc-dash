# Python Dash Reference Card

## Table of Contents
1. [Getting Started](#getting-started)
2. [Core Components](#core-components)
3. [HTML Components](#html-components)
4. [Layout Structure](#layout-structure)
5. [Basic Callbacks](#basic-callbacks)
6. [Advanced Callbacks](#advanced-callbacks)
7. [Data Visualization](#data-visualization)
8. [Application State & URL Routing](#application-state--url-routing)
9. [Performance Optimization](#performance-optimization)
10. [Deployment](#deployment)
11. [Best Practices](#best-practices)
12. [Common Patterns](#common-patterns)

## Getting Started

### Installation
```python
pip install dash
pip install dash-bootstrap-components  # Optional but recommended
pip install pandas plotly              # Common dependencies
```

### Minimal Application
```python
from dash import Dash, html, dcc
import plotly.express as px
import pandas as pd

# Initialize the app
app = Dash(__name__)

# Define layout
app.layout = html.Div([
    html.H1("Hello Dash"),
    dcc.Graph(
        id='example-graph',
        figure=px.scatter(
            pd.DataFrame({
                'x': [1, 2, 3, 4],
                'y': [2, 1, 3, 4],
                'size': [10, 20, 30, 40]
            }), 
            x='x', y='y', size='size'
        )
    )
])

# Run the app
if __name__ == '__main__':
    app.run_server(debug=True)
```

## Core Components

Core components (imported via `from dash import dcc`) provide interactive UI elements.

| Component | Description | Basic Example |
|-----------|-------------|---------------|
| `dcc.Graph` | Plotly graph | `dcc.Graph(id='graph', figure=fig)` |
| `dcc.Dropdown` | Selection dropdown | `dcc.Dropdown(options=[{'label': 'A', 'value': 'a'}], value='a')` |
| `dcc.Slider` | Slider control | `dcc.Slider(min=0, max=10, step=1, value=5)` |
| `dcc.RangeSlider` | Range slider | `dcc.RangeSlider(min=0, max=10, value=[3, 7])` |
| `dcc.Input` | Text input | `dcc.Input(type='text', value='initial value')` |
| `dcc.Textarea` | Multiline text input | `dcc.Textarea(value='Text area content')` |
| `dcc.Checklist` | Multiple checkbox | `dcc.Checklist(options=[{'label': 'A', 'value': 'a'}], value=['a'])` |
| `dcc.RadioItems` | Radio buttons | `dcc.RadioItems(options=[{'label': 'A', 'value': 'a'}], value='a')` |
| `dcc.DatePickerSingle` | Single date picker | `dcc.DatePickerSingle(date=date(2020, 1, 1))` |
| `dcc.DatePickerRange` | Date range picker | `dcc.DatePickerRange(start_date=date(2020, 1, 1))` |
| `dcc.Upload` | File upload | `dcc.Upload(children=html.Button('Upload File'))` |
| `dcc.Tabs` | Tab container | `dcc.Tabs([dcc.Tab(label='Tab 1', children=[...]), dcc.Tab(label='Tab 2', children=[...])])` |
| `dcc.Store` | Client-side storage | `dcc.Store(id='storage', data={'key': 'value'})` |
| `dcc.Interval` | Periodic callback | `dcc.Interval(id='interval', interval=1000)` |
| `dcc.Location` | URL monitoring | `dcc.Location(id='url')` |

## HTML Components

HTML components (imported via `from dash import html`) represent HTML tags.

```python
# Common html components
html.Div(children=[...], className='container')  # <div class="container">...</div>
html.H1("Title")                                 # <h1>Title</h1>
html.P("Paragraph")                              # <p>Paragraph</p>
html.A("Link", href="https://example.com")       # <a href="https://example.com">Link</a>
html.Img(src="/assets/image.png")                # <img src="/assets/image.png">
html.Button("Click Me", id="button")             # <button id="button">Click Me</button>
html.Br()                                        # <br>
html.Hr()                                        # <hr>
html.Table([                                     # <table>...</table>
    html.Thead(html.Tr([html.Th("Col1"), html.Th("Col2")])),
    html.Tbody([
        html.Tr([html.Td("row 1, col 1"), html.Td("row 1, col 2")]),
        html.Tr([html.Td("row 2, col 1"), html.Td("row 2, col 2")])
    ])
])
```

## Layout Structure

### Container Patterns
```python
# Vertical stack (common layout pattern)
app.layout = html.Div([
    html.Div([...], className='header'),
    html.Div([...], className='filters'),
    html.Div([...], className='content'),
    html.Div([...], className='footer')
], className='container')

# Horizontal layout (using flexbox)
row = html.Div([
    html.Div([...], className='column', style={'flex': '1'}),
    html.Div([...], className='column', style={'flex': '2'})
], style={'display': 'flex'})

# Grid layout
grid = html.Div([
    html.Div([...], className='cell'),
    html.Div([...], className='cell'),
    html.Div([...], className='cell'),
    html.Div([...], className='cell')
], style={'display': 'grid', 'gridTemplateColumns': 'repeat(2, 1fr)'})
```

### Using Dash Bootstrap Components
```python
import dash_bootstrap_components as dbc

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

app.layout = dbc.Container([
    dbc.Row([
        dbc.Col(html.H1("Dashboard Title"), width=12)
    ]),
    dbc.Row([
        dbc.Col([...], width=4),  # Sidebar
        dbc.Col([...], width=8)   # Main content
    ])
])
```

## Basic Callbacks

Callbacks connect component properties to create interactive behavior.

### Basic Callback Syntax
```python
from dash import Dash, html, dcc, Input, Output, State, callback

app.layout = html.Div([
    dcc.Input(id="input-value", type="text", value=""),
    html.Button("Submit", id="submit-button"),
    html.Div(id="output-div")
])

@callback(
    Output("output-div", "children"),
    Input("submit-button", "n_clicks"),
    State("input-value", "value")
)
def update_output(n_clicks, input_value):
    if n_clicks is None:
        return ""
    return f"You've entered: {input_value}"
```

### Multiple Inputs
```python
@callback(
    Output("result", "children"),
    Input("slider-1", "value"),
    Input("slider-2", "value")
)
def calculate_sum(value1, value2):
    return f"Sum: {value1 + value2}"
```

### Multiple Outputs
```python
@callback(
    Output("output-1", "children"),
    Output("output-2", "style"),
    Input("input-value", "value")
)
def update_multiple_outputs(value):
    return (
        f"Current value: {value}",
        {'color': 'green' if int(value) > 0 else 'red'}
    )
```

## Advanced Callbacks

### Pattern-Matching Callbacks
For dynamically created components with IDs that follow a pattern:

```python
from dash.dependencies import MATCH, ALL

app.layout = html.Div([
    html.Button("Add Filter", id="add-filter"),
    html.Div(id="filters-container", children=[])
])

@callback(
    Output("filters-container", "children"),
    Input("add-filter", "n_clicks"),
    State("filters-container", "children")
)
def add_filter(n_clicks, existing_filters):
    if n_clicks is None:
        return []
    new_filter = html.Div([
        dcc.Dropdown(
            id={"type": "filter-dropdown", "index": n_clicks},
            options=[{"label": f"Option {i}", "value": i} for i in range(5)]
        ),
        html.Div(id={"type": "filter-output", "index": n_clicks})
    ])
    return existing_filters + [new_filter]

@callback(
    Output({"type": "filter-output", "index": MATCH}, "children"),
    Input({"type": "filter-dropdown", "index": MATCH}, "value")
)
def display_output(value):
    return f"Selected value: {value}"
```

### Clientside Callbacks
Perform callback logic in JavaScript for improved performance:

```python
app.clientside_callback(
    """
    function(value) {
        return 'Slider value: ' + value;
    }
    """,
    Output("output-div", "children"),
    Input("slider", "value")
)
```

### PreventUpdate and no_update
Control when callbacks execute:

```python
from dash.exceptions import PreventUpdate

@callback(
    Output("output", "children"),
    Input("input", "value")
)
def update_output(value):
    if value is None or value == "":
        raise PreventUpdate
    return f"Input value: {value}"
```

To update some outputs but not others:

```python
from dash import no_update

@callback(
    Output("output1", "children"),
    Output("output2", "children"),
    Input("input", "value")
)
def update_some_outputs(value):
    if value < 0:
        return f"Negative value: {value}", no_update
    return f"Value: {value}", f"Positive value!"
```

## Data Visualization

### Creating Plotly Figures
```python
import plotly.express as px
import plotly.graph_objects as go
import pandas as pd

# Using Plotly Express (simplified high-level API)
df = pd.DataFrame({
    'x': [1, 2, 3, 4],
    'y': [10, 11, 12, 13],
    'category': ['A', 'B', 'A', 'B']
})

# Express figure
fig_express = px.scatter(
    df, x='x', y='y', color='category',
    title='Scatter Plot', 
    labels={'x': 'X Axis', 'y': 'Y Axis'}
)

# Graph Objects figure (more control)
fig_go = go.Figure()
fig_go.add_trace(go.Scatter(
    x=df['x'], 
    y=df['y'],
    mode='markers',
    marker=dict(size=12, color='blue'),
    name='Data Points'
))
fig_go.update_layout(
    title='Custom Scatter Plot',
    xaxis_title='X Axis',
    yaxis_title='Y Axis'
)

# Use in layout
app.layout = html.Div([
    dcc.Graph(id='express-graph', figure=fig_express),
    dcc.Graph(id='go-graph', figure=fig_go)
])
```

### Updating Graphs via Callbacks
```python
@callback(
    Output('graph', 'figure'),
    Input('dropdown', 'value')
)
def update_graph(selected_category):
    filtered_df = df[df['category'] == selected_category]
    
    fig = px.scatter(
        filtered_df, x='x', y='y',
        title=f'Data for {selected_category}'
    )
    
    # Customize the figure
    fig.update_layout(
        height=500,
        xaxis={'title': 'X Value'},
        yaxis={'title': 'Y Value'}
    )
    
    return fig
```

### Figure Customization
```python
fig = px.line(df, x='date', y='value')

# Add custom elements
fig.add_hline(y=100, line_dash="dash", line_color="red")
fig.add_annotation(x="2020-01-15", y=120, text="Peak Value")

# Update layout
fig.update_layout(
    title="Time Series Data",
    template="plotly_white",  # Use a template
    legend=dict(
        orientation="h",
        yanchor="bottom",
        y=1.02,
        xanchor="right",
        x=1
    ),
    margin=dict(l=40, r=40, t=50, b=40)
)

# Update axes
fig.update_xaxes(
    title="Date",
    showgrid=True, 
    gridwidth=1, 
    gridcolor='LightGray'
)

fig.update_yaxes(
    title="Value",
    range=[0, 150]
)
```

## Application State & URL Routing

### URL Routing
```python
from dash import Dash, html, dcc, Input, Output, callback

app = Dash(__name__, suppress_callback_exceptions=True)

# Layout with URL routing
app.layout = html.Div([
    dcc.Location(id='url', refresh=False),
    html.Div(id='page-content')
])

# Page layouts
home_layout = html.Div([
    html.H1('Home Page'),
    dcc.Link('Go to Page 1', href='/page-1'),
    html.Br(),
    dcc.Link('Go to Page 2', href='/page-2')
])

page1_layout = html.Div([
    html.H1('Page 1'),
    dcc.Link('Go to Home', href='/')
])

page2_layout = html.Div([
    html.H1('Page 2'),
    dcc.Link('Go to Home', href='/')
])

# Update page content based on URL
@callback(
    Output('page-content', 'children'),
    Input('url', 'pathname')
)
def display_page(pathname):
    if pathname == '/page-1':
        return page1_layout
    elif pathname == '/page-2':
        return page2_layout
    else:
        return home_layout
```

### Client-Side Storage
```python
from dash import Dash, html, dcc, Input, Output, State, callback

app.layout = html.Div([
    dcc.Store(id='local-storage', storage_type='local'),  # 'local', 'session', or 'memory'
    html.Button('Save Data', id='save-button'),
    html.Div(id='output')
])

@callback(
    Output('local-storage', 'data'),
    Input('save-button', 'n_clicks'),
    State('local-storage', 'data'),
    prevent_initial_call=True
)
def save_to_storage(n_clicks, current_data):
    if current_data is None:
        current_data = {'clicks': 0}
    
    current_data['clicks'] = current_data.get('clicks', 0) + 1
    return current_data

@callback(
    Output('output', 'children'),
    Input('local-storage', 'data')
)
def display_storage_data(data):
    if data is None:
        return "No data saved yet"
    return f"You've clicked the save button {data.get('clicks', 0)} times"
```

## Performance Optimization

### Using `dcc.Loading`
Wrap components with loading indicators:

```python
app.layout = html.Div([
    dcc.Loading(
        id="loading",
        type="default",  # "default", "circle", "cube", "dot", or "graph"
        children=html.Div(id="loading-output")
    ),
    html.Button('Load Data', id='load-button')
])

@callback(
    Output("loading-output", "children"),
    Input("load-button", "n_clicks")
)
def load_data(n_clicks):
    if n_clicks is None:
        return "Click the button to load data"
    
    # Simulate a long computation
    import time
    time.sleep(2)
    
    return "Data loaded successfully!"
```

### Using Callbacks with `prevent_initial_call`
Prevent callbacks from firing at initialization:

```python
@callback(
    Output("output", "children"),
    Input("button", "n_clicks"),
    prevent_initial_call=True
)
def on_button_click(n_clicks):
    return f"Button clicked {n_clicks} times"
```

### Caching Data
```python
import flask
from dash import Dash, html, dcc, Input, Output, callback
import pandas as pd
from functools import lru_cache

server = flask.Flask(__name__)
app = Dash(__name__, server=server)

@lru_cache(maxsize=32)
def get_data(filename):
    # This function will only execute once per unique filename
    # and cache the result for future calls
    return pd.read_csv(filename)

@callback(
    Output('graph', 'figure'),
    Input('dataset-dropdown', 'value')
)
def update_graph(selected_dataset):
    # Get data using cached function
    df = get_data(f'data/{selected_dataset}.csv')
    
    fig = px.scatter(df, x='x', y='y')
    return fig
```

## Deployment

### Basic Deployment Structure
```python
# app.py
from dash import Dash, html
import dash_bootstrap_components as dbc

# Initialize the app with a custom server for production
from flask import Flask
server = Flask(__name__)
app = Dash(__name__, server=server, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Define app layout
app.layout = html.Div([...])

# Define callbacks
# ...

# Run the server
if __name__ == '__main__':
    app.run_server(debug=False, host='0.0.0.0', port=8050)
```

### Heroku Configuration
Create a `Procfile`:
```
web: gunicorn app:server
```

Create a `requirements.txt`:
```
dash==2.11.1
dash-bootstrap-components==1.4.1
pandas==2.0.3
plotly==5.15.0
gunicorn==21.2.0
```

### Docker Configuration
Create a `Dockerfile`:
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8050

CMD ["gunicorn", "--bind", "0.0.0.0:8050", "app:server"]
```

## Best Practices

### Code Organization
```
my_dash_app/
├── app.py             # Main application file
├── layouts/           # Layout components
│   ├── __init__.py
│   ├── home.py
│   └── details.py
├── callbacks/         # Callback logic
│   ├── __init__.py
│   ├── data_processing.py
│   └── visualization.py
├── assets/            # Static assets
│   ├── custom.css
│   └── logo.png
├── data/              # Data files or data access layer
│   └── process_data.py
└── utils/             # Utility functions
    └── helpers.py
```

Example app structure with modular components:

```python
# app.py
from dash import Dash
import dash_bootstrap_components as dbc

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])
server = app.server

# Import layouts after initializing app
from layouts import create_layout
app.layout = create_layout(app)

# Import callbacks
from callbacks import register_callbacks
register_callbacks(app)

if __name__ == '__main__':
    app.run_server(debug=True)

# layouts/__init__.py
from dash import html
import dash_bootstrap_components as dbc
from layouts.home import home_layout
from layouts.details import details_layout

def create_layout(app):
    return html.Div([
        dbc.NavbarSimple(
            brand="My Dash App",
            color="primary",
            dark=True,
        ),
        html.Div(id='page-content', className='container mt-4'),
        # More layout elements
    ])

# callbacks/__init__.py
def register_callbacks(app):
    from callbacks.data_processing import register_data_callbacks
    from callbacks.visualization import register_viz_callbacks
    
    register_data_callbacks(app)
    register_viz_callbacks(app)
```

### Error Handling
```python
@callback(
    Output("output", "children"),
    Input("input", "value")
)
def process_input(value):
    try:
        # Attempt processing
        result = some_risky_operation(value)
        return f"Result: {result}"
    except ValueError:
        # Handle specific error
        return "Please enter a valid number"
    except Exception as e:
        # Log the error (in a production environment)
        print(f"Error processing input: {str(e)}")
        return "An error occurred during processing"
```

### Debugging Tips
```python
# Enable verbose logging
import logging
log = logging.getLogger('werkzeug')
log.setLevel(logging.ERROR)

# Run app with debug enabled
app.run_server(debug=True)

# Add debug prints in callbacks
def my_callback(input_value):
    print(f"Callback triggered with value: {input_value}")
    # Callback logic
    return output
```

## Common Patterns

### Responsive Layout (using dbc)
```python
import dash_bootstrap_components as dbc
from dash import Dash, html, dcc

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

app.layout = dbc.Container([
    dbc.Row([
        dbc.Col(html.H1("Responsive Dashboard"), width=12)
    ]),
    
    dbc.Row([
        # Sidebar - collapses on small screens
        dbc.Col([
            html.Div("Filters", className="h3"),
            dcc.Dropdown(id="filter-dropdown"),
            html.Hr(),
            dcc.Checklist(id="options-checklist")
        ], width=12, lg=3),
        
        # Main content
        dbc.Col([
            # Two charts in a row - stack on small screens
            dbc.Row([
                dbc.Col(dcc.Graph(id="chart1"), width=12, lg=6),
                dbc.Col(dcc.Graph(id="chart2"), width=12, lg=6)
            ]),
            
            # Full width chart
            dbc.Row([
                dbc.Col(dcc.Graph(id="chart3"), width=12)
            ])
        ], width=12, lg=9)
    ])
], fluid=True)
```

### Data Table with Filtering and Pagination
```python
from dash import Dash, html, dash_table, Input, Output, callback
import pandas as pd

# Sample data
df = pd.DataFrame({
    'Country': ['USA', 'Canada', 'Mexico', 'UK', 'France', 'Germany'],
    'Population': [331002651, 37742154, 126190788, 67886011, 65273511, 83783942],
    'GDP': [21433225, 1736425, 1076163, 2827113, 2715518, 3846702]
})

app = Dash(__name__)

app.layout = html.Div([
    html.H3("Interactive Data Table"),
    
    html.Div([
        html.Label("Search:"),
        dcc.Input(id="search-input", type="text", placeholder="Filter data...")
    ]),
    
    dash_table.DataTable(
        id='data-table',
        columns=[{"name": col, "id": col} for col in df.columns],
        data=df.to_dict('records'),
        filter_action="custom",
        page_action="native",
        page_size=10,
        style_table={'overflowX': 'auto'},
        style_cell={
            'textAlign': 'left',
            'minWidth': '100px', 'width': '150px', 'maxWidth': '200px',
            'overflow': 'hidden',
            'textOverflow': 'ellipsis'
        },
        style_header={
            'backgroundColor': 'rgb(230, 230, 230)',
            'fontWeight': 'bold'
        }
    )
])

@callback(
    Output('data-table', 'data'),
    Input('search-input', 'value')
)
def filter_table(search_term):
    if search_term is None or search_term == '':
        return df.to_dict('records')
    
    # Filter dataframe based on search term
    filtered_df = df[df.apply(
        lambda row: any(
            str(search_term).lower() in str(cell).lower() 
            for cell in row
        ), 
        axis=1
    )]
    
    return filtered_df.to_dict('records')
```

### Collapsible Sidebar
```python
import dash_bootstrap_components as dbc
from dash import Dash, html, Input, Output, State, callback

app = Dash(__name__, external_stylesheets=[dbc.themes.BOOTSTRAP])

# Define sidebar content
sidebar = html.Div(
    [
        html.H2("Sidebar", className="display-4"),
        html.Hr(),
        html.P("A responsive sidebar with collapsible content", className="lead"),
        dbc.Nav(
            [
                dbc.NavLink("Home", href="#", active="exact"),
                dbc.NavLink("Page 1", href="#", active="exact"),
                dbc.NavLink("Page 2", href="#", active="exact"),
            ],
            vertical=True,
            pills=True,
        ),
    ],
    id="sidebar",
    style={
        "position": "fixed",
        "top": 0,
        "left": 0,
        "bottom": 0,
        "width": "16rem",
        "padding": "2rem 1rem",
        "backgroundColor": "#f8f9fa",
    }
)

# Define content area
content = html.Div(
    [
        html.Div(
            [
                html.Button(
                    "Toggle Sidebar",
                    id="toggle-button",
                    className="btn btn-primary",
                )
            ],
            style={"padding": "1rem 1rem"}
        ),
        html.Div(id="page-content", style={"padding": "1rem 1rem"})
    ],
    id="content",
    style={
        "marginLeft": "18rem",
        "padding": "2rem 1rem",
        "transition": "margin-left .5s"
    }
)

app.layout = html.Div([sidebar, content])

@callback(
    [Output("sidebar", "style"), 
     Output("content", "style"),
     Output("toggle-button", "children")],
    [Input("toggle-button", "n_clicks")],
    [State("sidebar", "style"), 
     State("content", "style")]
)
def toggle_sidebar(n_clicks, sidebar_style, content_style):
    if n_clicks and n_clicks % 2 == 1:
        # Sidebar is hidden
        sidebar_style.update({"marginLeft": "-16rem"})
        content_style.update({"marginLeft": "2rem"})
        button_text = "Show Sidebar"
    else:
        # Sidebar is shown
        sidebar_style.update({"marginLeft": "0rem"})
        content_style.update({"marginLeft": "18rem"})
        button_text = "Hide Sidebar"
    
    return sidebar_style, content_style, button_text
```

### Real-time Data Updates
```python
from dash import Dash, html, dcc, Input, Output, callback
import plotly.graph_objects as go
from datetime import datetime
import random

app = Dash(__name__)

# Initialize data
initial_time = datetime.now()
times = [initial_time]
values = [random.randint(0, 100)]

app.layout = html.Div([
    html.H1("Real-time Data Visualization"),
    
    # Interval component for updates every second
    dcc.Interval(
        id='interval-component',
        interval=1*1000,  # in milliseconds
        n_intervals=0
    ),
    
    dcc.Graph(id='live-graph'),
    
    html.Button('Pause/Resume', id='pause-button'),
    
    # Store to track paused state
    dcc.Store(id='paused-store', data={'paused': False})
])

@callback(
    Output('paused-store', 'data'),
    Input('pause-button', 'n_clicks'),
    State('paused-store', 'data')
)
def toggle_pause(n_clicks, data):
    if n_clicks:
        data['paused'] = not data['paused']
    return data

@callback(
    Output('live-graph', 'figure'),
    Input('interval-component', 'n_intervals'),
    State('paused-store', 'data')
)
def update_graph(n, data):
    global times, values
    
    # Only add new data if not paused
    if not data['paused']:
        times.append(datetime.now())
        values.append(values[-1] + random.randint(-10, 10))
        
        # Keep only the last 100 data points
        if len(times) > 100:
            times = times[-100:]
            values = values[-100:]
    
    # Create figure
    fig = go.Figure(data=go.Scatter(
        x=times,
        y=values,
        mode='lines+markers',
        name='Value'
    ))
    
    fig.update_layout(
        title='Live Random Data',
        xaxis_title='Time',
        yaxis_title='Value'
    )
    
    return fig
```
