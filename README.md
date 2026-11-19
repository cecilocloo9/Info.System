# Info.System
"""Text Encoder / Decoder
Algorithms: Caesar, ROT13, Vigenere, Atbash, Base64, Hex, Morse
Run:  python text_codec.py
"""
import base64

# ---------- Caesar / ROT13 ----------
def _shift(text, k):
    out = []
    for ch in text:
        if ch.isalpha():
            base = ord('A') if ch.isupper() else ord('a')
            out.append(chr((ord(ch) - base + k) % 26 + base))
        else:
            out.append(ch)
    return "".join(out)

def caesar_encode(t, key): return _shift(t, int(key))
def caesar_decode(t, key): return _shift(t, -int(key))
def rot13(t, key=None): return _shift(t, 13)

# ---------- Vigenere ----------
def _vig(text, key, sign):
    key = [ord(c.lower()) - 97 for c in key if c.isalpha()]
    if not key:
        raise ValueError("Key must contain letters.")
    out, i = [], 0
    for ch in text:
        if ch.isalpha():
            base = ord('A') if ch.isupper() else ord('a')
            out.append(chr((ord(ch) - base + sign * key[i % len(key)]) % 26 + base))
            i += 1
        else:
            out.append(ch)
    return "".join(out)

def vigenere_encode(t, key): return _vig(t, key, 1)
def vigenere_decode(t, key): return _vig(t, key, -1)

# ---------- Atbash ----------
def atbash(t, key=None):
    out = []
    for ch in t:
        if ch.isalpha():
            base = ord('A') if ch.isupper() else ord('a')
            out.append(chr(base + 25 - (ord(ch) - base)))
        else:
            out.append(ch)
    return "".join(out)

# ---------- Base64 / Hex ----------
def b64_encode(t, key=None): return base64.b64encode(t.encode()).decode()
def b64_decode(t, key=None): return base64.b64decode(t.encode()).decode()
def hex_encode(t, key=None): return t.encode().hex()
def hex_decode(t, key=None): return bytes.fromhex(t).decode()

# ---------- Morse ----------
MORSE = {
    'A': '.-', 'B': '-...', 'C': '-.-.', 'D': '-..', 'E': '.', 'F': '..-.',
    'G': '--.', 'H': '....', 'I': '..', 'J': '.---', 'K': '-.-', 'L': '.-..',
    'M': '--', 'N': '-.', 'O': '---', 'P': '.--.', 'Q': '--.-', 'R': '.-.',
    'S': '...', 'T': '-', 'U': '..-', 'V': '...-', 'W': '.--', 'X': '-..-',
    'Y': '-.--', 'Z': '--..', '0': '-----', '1': '.----', '2': '..---',
    '3': '...--', '4': '....-', '5': '.....', '6': '-....', '7': '--...',
    '8': '---..', '9': '----.',
}
REV_MORSE = {v: k for k, v in MORSE.items()}

def morse_encode(t, key=None):
    return " / ".join(" ".join(MORSE.get(c, '?') for c in w) for w in t.upper().split(" "))

def morse_decode(t, key=None):
    return " ".join("".join(REV_MORSE.get(c, '?') for c in w.split()) for w in t.split(" / "))

# ---------- Menu ----------
# name: (encode, decode, needs_key)
ALGORITHMS = {
    "1": ("Caesar cipher", caesar_encode, caesar_decode, True),
    "2": ("ROT13", rot13, rot13, False),
    "3": ("Vigenere cipher", vigenere_encode, vigenere_decode, True),
    "4": ("Atbash", atbash, atbash, False),
    "5": ("Base64", b64_encode, b64_decode, False),
    "6": ("Hex", hex_encode, hex_decode, False),
    "7": ("Morse code", morse_encode, morse_decode, False),
}

def main():
    while True:
        print("\n=== Text Encoder / Decoder ===")
        for k, (name, *_ ) in ALGORITHMS.items():
            print(f"{k}. {name}")
        print("0. Exit")
        choice = input("Algorithm: ").strip()
        if choice == "0":
            break
        if choice not in ALGORITHMS:
            print("Invalid choice.")
            continue
        name, enc, dec, needs_key = ALGORITHMS[choice]
        mode = input("(E)ncode or (D)ecode? ").strip().lower()
        if mode not in ("e", "d"):
            print("Invalid mode.")
            continue
        key = input("Key: ").strip() if needs_key else None
        text = input("Text: ")
        try:
            result = (enc if mode == "e" else dec)(text, key)
            print(f"\n{name} result:\n{result}")
        except Exception as e:
            print(f"Error: {e}")

if __name__ == "__main__":
    main()
