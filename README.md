
# Development README

## Overview

This document provides an overview of the widget-related functionalities in the `widget.py` file and how to use them. It also includes code samples and an example of integrating a widget into an HTML file.

![admin](https://cdn.dribbble.com/userupload/13028668/file/original-a9166bf7c9ca9544813928b26880462c.png?resize=1024x500)

## Widget Functions

### `generate_widget`

The `generate_widget` function generates an embed code for a widget based on the provided campaign and game IDs. This embed code can be used to include the widget in any HTML page.

#### Endpoint

- **URL**: `/generate_widget/<int:campaign_id>/<int:game_id>`
- **Method**: `GET`
- **Parameters**:
  - `campaign_id` (int): The ID of the campaign.
  - `game_id` (int): The ID of the game.

#### Example Request

```sh
curl http://localhost:5000/generate_widget/1/1
```

#### Example Response

```json
{
  "embed_code": "<script src=\"/static/js/widget.js\" data-campaign-id=\"1\" data-game-id=\"1\"></script>"
}
```

### Code Explanation

```python
# app/routes/widget.py
from flask import Blueprint, jsonify, request
from app.models import Widget
from app import db

bp = Blueprint('widget', __name__)

@bp.route('/generate_widget/<int:campaign_id>/<int:game_id>')
def generate_widget(campaign_id, game_id):
    embed_code = f'<script src="/static/js/widget.js" data-campaign-id="{campaign_id}" data-game-id="{game_id}"></script>'
    new_widget = Widget(campaign_id=campaign_id, embed_code=embed_code)
    db.session.add(new_widget)
    db.session.commit()
    return jsonify({'embed_code': embed_code})
```

- **Blueprint**: The `Blueprint` object is used to organize the routes.
- **generate_widget**: This function generates an embed code for a widget and saves it to the database.
- **embed_code**: The generated embed code includes a script tag with the campaign and game IDs as data attributes.
- **Database Operations**: The new widget is added to the database and committed.

## Using the Widget

To use the generated widget, you can include the embed code in any HTML file. For example, let's say you have a `rock-paper-scissors.html` file.

### Example HTML File

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Rock Paper Scissors</title>
</head>
<body>
    <h1>Rock Paper Scissors Game</h1>
    <!-- Include the generated widget embed code here -->
    <script src="/static/js/widget.js" data-campaign-id="1" data-game-id="1"></script>
</body>
</html>
```

### Example JavaScript File (`widget.js`)

You need to create a `widget.js` file in the `static/js` directory to handle the widget functionality.

```javascript
// static/js/widget.js

document.addEventListener('DOMContentLoaded', function() {
    const scriptTag = document.currentScript;
    const campaignId = scriptTag.getAttribute('data-campaign-id');
    const gameId = scriptTag.getAttribute('data-game-id');

    // Example: Fetch game data and display it
    fetch(`/api/game/${campaignId}/${gameId}`)
        .then(response => response.json())
        .then(data => {
            // Display game data in the widget
            const gameContainer = document.createElement('div');
            gameContainer.innerHTML = `
                <h2>${data.game_name}</h2>
                <p>${data.description}</p>
                <!-- Add game-specific HTML and JavaScript here -->
            `;
            document.body.appendChild(gameContainer);
        })
        .catch(error => console.error('Error fetching game data:', error));
});
```

This JavaScript file fetches game data based on the campaign and game IDs and displays it in the widget.

## Conclusion

This document provides an overview of the widget-related functionalities in the `widget.py` file and how to use them. By following the examples provided, you can generate and integrate widgets into your HTML pages easily.

For more details, please refer to the main documentation. If you have any questions, feel free to reach out to our support team.
