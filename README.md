# Python
# -----------------------------------------------
# Python CLI Calculator with History (Safe Eval)
# -----------------------------------------------
# ✅ What this code does:
# - Safely evaluates math expressions (+, -, *, /, %, //, **)
# - Solves multiple comma-separated expressions at once
# - Saves calculation history to a text file
# - Supports 'show' to view history and 'clear' to reset it
# - Uses AST (Abstract Syntax Tree) for secure parsing (no eval risk)

# ✅ Features:
# - Safe evaluation using Python AST (no unsafe eval)
# - File-based history logging (persistent)
# - Supports commands: show, clear, exit
# - Clean and readable output

import ast
import operator
import os

HISTORY_FILE = "history.txt"

# Safe operators mapping
OPERATORS = {
    ast.Add: operator.add,
    ast.Sub: operator.sub,
    ast.Mult: operator.mul,
    ast.Div: operator.truediv,
    ast.Pow: operator.pow,
    ast.Mod: operator.mod,
    ast.FloorDiv: operator.floordiv,
    ast.USub: operator.neg,
}

# Evaluate expression safely
def safe_eval(expr):
    def _eval(node):
        if isinstance(node, ast.Num):
            return node.n
        elif isinstance(node, ast.UnaryOp):
            return OPERATORS[type(node.op)](_eval(node.operand))
        elif isinstance(node, ast.BinOp):
            return OPERATORS[type(node.op)](_eval(node.left), _eval(node.right))
        else:
            raise ValueError("Unsupported expression")
    try:
        tree = ast.parse(expr, mode='eval')
        return _eval(tree.body)
    except Exception as e:
        return f"Error: {e}"

# Show history from file
def show_history():
    if not os.path.exists(HISTORY_FILE) or os.stat(HISTORY_FILE).st_size == 0:
        print("📜 History is empty.\n")
    else:
        print("\n📜 Calculation History:")
        with open(HISTORY_FILE, 'r') as f:
            for line in f:
                print("  ", line.strip())
        print()

# Clear history file
def clear_history():
    open(HISTORY_FILE, 'w').close()
    print("🧹 History cleared.\n")

# Append to history file
def append_to_history(record):
    with open(HISTORY_FILE, 'a') as f:
        f.write(record + "\n")

# Main calculator
def calculator():
    print("📟 Welcome to Python CLI Calculator!")
    print("===================================")
    print("🧠 Features:")
    print("1️⃣  Solve multiple equations at once (comma separated)")
    print("2️⃣  Supports +, -, *, /, %, //, **, and brackets")
    print("3️⃣  View and save calculation history to file")
    print("4️⃣  Clear history file anytime")
    print("5️⃣  Commands:")
    print("    - Type `show`   → Show history")
    print("    - Type `clear`  → Clear history")
    print("    - Type `exit`   → Exit calculator")
    print("===================================\n")

    while True:
        user_input = input("📝 Enter equations or command: ").strip()

        if user_input.lower() == 'exit':
            print("👋 Goodbye! Thanks for using the calculator.")
            break
        elif user_input.lower() == 'show':
            show_history()
        elif user_input.lower() == 'clear':
            clear_history()
        else:
            expressions = [expr.strip() for expr in user_input.split(',') if expr.strip()]
            for expr in expressions:
                result = safe_eval(expr)
                record = f"{expr} = {result}"
                append_to_history(record)
                print("➡️ ", record)
            print()

# Run the calculator
if __name__ == "__main__":
    calculator()
