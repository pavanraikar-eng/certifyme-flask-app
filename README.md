from flask import Flask, request, jsonify

app = Flask(__name__)

# Store tasks in memory (like a temporary database)
tasks = []
task_id = 1

@app.route('/')
def home():
    return jsonify({
        "message": "Welcome to CertifyMe Task API",
        "status": "running",
        "endpoints": ["/getTasks", "/addTask", "/deleteTask/<id>"]
    })

@app.route('/getTasks', methods=['GET'])
def get_tasks():
    return jsonify({
        "tasks": tasks,
        "count": len(tasks)
    })

@app.route('/addTask', methods=['POST'])
def add_task():
    global task_id
    data = request.json
    
    if not data or 'title' not in data:
        return jsonify({"error": "Title is required"}), 400
    
    new_task = {
        "id": task_id,
        "title": data['title'],
        "description": data.get('description', ''),
        "completed": False
    }
    tasks.append(new_task)
    task_id += 1
    
    return jsonify({"message": "Task added", "task": new_task}), 201

@app.route('/deleteTask/<int:id>', methods=['DELETE'])
def delete_task(id):
    global tasks
    task_to_delete = None
    for task in tasks:
        if task['id'] == id:
            task_to_delete = task
            break
    
    if task_to_delete:
        tasks.remove(task_to_delete)
        return jsonify({"message": f"Task {id} deleted"})
    else:
        return jsonify({"error": "Task not found"}), 404

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
