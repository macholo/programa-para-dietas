# -*- coding: utf-8 -*-
"""
Plan alimentario Nicaragua — PAN inspirado en caso clínico (Schofield/GEB, 5 tiempos,
ajustes por actividad, objetivo y patologías). No sustituye valoración profesional.
"""
import json
import re
import tkinter as tk
from datetime import datetime
from pathlib import Path
from tkinter import filedialog, messagebox, ttk
from uuid import uuid4

try:
    from fpdf import FPDF
    _HAS_FPDF = True
except ImportError:
    FPDF = None
    _HAS_FPDF = False

# === Rutas de datos ===
_DATA_DIR = Path(__file__).resolve().parent / "data"
_PACIENTES_JSON = _DATA_DIR / "pacientes.json"

# === Menú semanal (5 tiempos) — alimentos típicos Nicaragua ===
MENU_BASE = {
    "Verano": {
        "Lunes": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Naranja", "Sandía", "Banano"],
            "Almuerzo": ["Pescado", "Arroz", "Ensalada", "Tiste"],
            "Merienda_PM": ["Papaya", "Piña", "fresco de frutas naturales"],
            "Cena": ["Queso", "Tortilla", "Sandía"],
        },
        "Martes": {
            "Desayuno": ["Gallo pinto", "Queso", "Tortilla", "Tiste"],
            "Merienda_AM": ["Melón", "Piña", "Naranja"],
            "Almuerzo": ["Vigorón", "Ensalada de repollo", "Melón", "Chicha de maíz"],
            "Merienda_PM": ["Papaya", "Sandía", "fresco de cacao"],
            "Cena": ["Ensaladas", "Tortilla", "Piña"],
        },
        "Miércoles": {
            "Desayuno": ["Gallo pinto seco", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Sandía", "Banano", "Naranja"],
            "Almuerzo": ["Pescado", "Arroz", "Ensalada", "Naranja", "fresco de frutas naturales"],
            "Merienda_PM": ["Melón", "Piña", "Papaya"],
            "Cena": ["Queso", "Tortilla", "Papaya"],
        },
        "Jueves": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Tiste"],
            "Merienda_AM": ["Papaya", "Sandía", "Naranja"],
            "Almuerzo": ["Pescado", "Arroz", "Ensalada", "Sandía"],
            "Merienda_PM": ["Piña", "Melón", "Banano"],
            "Cena": ["Queso", "Tortilla", "Melón"],
        },
        "Viernes": {
            "Desayuno": ["Gallo pinto", "Queso", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Naranja", "Piña", "Sandía"],
            "Almuerzo": ["Vigorón", "Ensalada de repollo", "Piña", "fresco de cacao"],
            "Merienda_PM": ["Papaya", "Melón", "fresco de frutas naturales"],
            "Cena": ["Ensaladas", "Tortilla", "Naranja"],
        },
        "Sábado": {
            "Desayuno": ["Nacatamal (fines de semana)", "Tiste"],
            "Merienda_AM": ["Papaya", "Sandía", "Banano"],
            "Almuerzo": ["Pescado", "Arroz", "Ensalada", "Papaya"],
            "Merienda_PM": ["Piña", "Naranja", "Melón"],
            "Cena": ["Queso", "Tortilla", "Sandía"],
        },
        "Domingo": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Melón", "Sandía", "Naranja"],
            "Almuerzo": ["Vigorón", "Ensalada de repollo", "Melón", "Naranja", "Chicha de maíz"],
            "Merienda_PM": ["Papaya", "Piña", "Banano"],
            "Cena": ["Queso", "Tortilla", "Piña"],
        },
    },
    "Invierno": {
        "Lunes": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Mango", "Papaya", "Naranja"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Mango"],
            "Merienda_PM": ["Piña", "Melón", "Sandía"],
            "Cena": ["Queso", "Tortilla", "Papaya"],
        },
        "Martes": {
            "Desayuno": ["Güirila con cuajada", "Tiste"],
            "Merienda_AM": ["Naranja", "Banano", "Papaya"],
            "Almuerzo": ["Sopa de gallina", "Arroz", "Ensalada", "Naranja"],
            "Merienda_PM": ["Sandía", "Melón", "Piña"],
            "Cena": ["Queso", "Tortilla", "Melón"],
        },
        "Miércoles": {
            "Desayuno": ["Gallo pinto seco", "Queso", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Mango", "Sandía", "Papaya"],
            "Almuerzo": ["Sopa de frijoles", "Arroz", "Ensalada", "Papaya"],
            "Merienda_PM": ["Naranja", "Piña", "Banano"],
            "Cena": ["Queso", "Tortilla", "Piña"],
        },
        "Jueves": {
            "Desayuno": ["Elote cocido", "Café con leche"],
            "Merienda_AM": ["Papaya", "Naranja", "Melón"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Mango"],
            "Merienda_PM": ["Sandía", "Piña", "Papaya"],
            "Cena": ["Queso", "Tortilla", "Melón"],
        },
        "Viernes": {
            "Desayuno": ["Gallo pinto", "Queso", "Tortilla", "Tiste"],
            "Merienda_AM": ["Mango", "Naranja", "Banano"],
            "Almuerzo": ["Sopa de gallina", "Ensalada de repollo", "Naranja", "Piña"],
            "Merienda_PM": ["Papaya", "Sandía", "Melón"],
            "Cena": ["Queso", "Tortilla", "Naranja"],
        },
        "Sábado": {
            "Desayuno": ["Nacatamal (fines de semana)", "Tiste"],
            "Merienda_AM": ["Piña", "Sandía", "Papaya"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Papaya"],
            "Merienda_PM": ["Naranja", "Melón", "Banano"],
            "Cena": ["Queso", "Tortilla", "Sandía"],
        },
        "Domingo": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Mango", "Papaya", "Sandía"],
            "Almuerzo": ["Sopa de gallina", "Ensalada de repollo", "Mango", "Chicha de maíz"],
            "Merienda_PM": ["Piña", "Naranja", "Melón"],
            "Cena": ["Queso", "Tortilla", "Piña"],
        },
    },
}

DATOS_ALIMENTO = {
    "Gallo pinto": {"kcal": 130, "porcion": "1/2 taza (120g)", "grupo": "carbohidrato"},
    "Gallo pinto seco": {"kcal": 120, "porcion": "1/2 taza (120g)", "grupo": "carbohidrato"},
    "Huevo frito": {"kcal": 90, "porcion": "1 unidad (50g)", "grupo": "proteina"},
    "Queso": {"kcal": 80, "porcion": "1 rebanada (30g)", "grupo": "proteina"},
    "Tortilla": {"kcal": 70, "porcion": "1 unidad (40g)", "grupo": "carbohidrato"},
    "Café con leche": {"kcal": 70, "porcion": "1 taza (240ml)", "grupo": "bebida"},
    "Tiste": {"kcal": 120, "porcion": "1 taza (240ml)", "grupo": "bebida"},
    "Pescado": {"kcal": 120, "porcion": "1 filete (80g)", "grupo": "proteina"},
    "Arroz": {"kcal": 110, "porcion": "1/2 taza (65g)", "grupo": "carbohidrato"},
    "Ensalada": {"kcal": 40, "porcion": "1 taza (80g)", "grupo": "verdura"},
    "Ensaladas": {"kcal": 35, "porcion": "1 taza (60g)", "grupo": "verdura"},
    "Ensalada de repollo": {"kcal": 40, "porcion": "1/2 taza (70g)", "grupo": "verdura"},
    "Sandía": {"kcal": 30, "porcion": "1 rodaja (150g)", "grupo": "fruta"},
    "Melón": {"kcal": 60, "porcion": "1 rodaja (150g)", "grupo": "fruta"},
    "Piña": {"kcal": 55, "porcion": "1 taza (140g)", "grupo": "fruta"},
    "Naranja": {"kcal": 60, "porcion": "1 mediana (130g)", "grupo": "fruta"},
    "Papaya": {"kcal": 55, "porcion": "1 taza (140g)", "grupo": "fruta"},
    "Banano": {"kcal": 90, "porcion": "1 unidad mediana (100g)", "grupo": "fruta"},
    "Vigorón": {"kcal": 300, "porcion": "1 porción (250g)", "grupo": "carbohidrato"},
    "Chicha de maíz": {"kcal": 100, "porcion": "1 vaso (250ml)", "grupo": "bebida"},
    "fresco de cacao": {"kcal": 100, "porcion": "1 vaso (200ml)", "grupo": "bebida"},
    "fresco de frutas naturales": {"kcal": 90, "porcion": "1 vaso (200ml)", "grupo": "bebida"},
    "Nacatamal (fines de semana)": {"kcal": 450, "porcion": "1 unidad (300g)", "grupo": "carbohidrato"},
    "Sopa de res": {"kcal": 150, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Sopa de gallina": {"kcal": 120, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Sopa de frijoles": {"kcal": 110, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Mango": {"kcal": 70, "porcion": "1 unidad (120g)", "grupo": "fruta"},
    "Elote cocido": {"kcal": 70, "porcion": "1 mazorca (90g)", "grupo": "carbohidrato"},
    "Güirila con cuajada": {"kcal": 180, "porcion": "1 unidad (100g)", "grupo": "carbohidrato"},
}

# Palabras clave por condición (filtrado de alimentos del menú)
PROHIBIDOS = {
    "diabetes": {"bebida", "azucar", "fresco", "tiste", "chicha", "cacao"},
    "hipertension": {"queso", "sal", "fresco", "embutido"},
    "obesidad": {"fritura", "grasa", "nacatamal", "vigoron"},
    "dislipidemia": {"fritura", "grasa", "vigoron", "nacatamal"},
    "gastritis": {"cafe", "picante", "alcohol", "fresco", "tiste", "chicha"},
    "anemia": set(),
    "cardiovascular": {"queso", "sal", "embutido", "fritura", "grasa"},
    "endocrino": {"bebida", "azucar", "fresco", "tiste", "chicha", "cacao", "gaseosa"},
    # Renal: evitar palabras genéricas como "fresco" (aparece en nombres de fruta/bebida)
    "renal": {"sal", "embutido", "chicha", "cacao"},
}

ORDEN_COMIDAS = ["Desayuno", "Merienda_AM", "Almuerzo", "Merienda_PM", "Cena"]

REGLAS_COMIDA = {
    "Desayuno": ["carbohidrato", "proteina"],
    "Merienda_AM": ["fruta", "verdura", "bebida"],
    "Almuerzo": ["proteina", "carbohidrato", "verdura"],
    "Merienda_PM": ["fruta", "verdura", "bebida"],
    "Cena": ["proteina", "verdura", "fruta"],
}

PORCION_X_COMIDA = {
    "Desayuno": 0.20,
    "Merienda_AM": 0.10,
    "Almuerzo": 0.35,
    "Merienda_PM": 0.10,
    "Cena": 0.25,
}

FACTOR_ACTIVIDAD = {
    "Sedentario": 1.2,
    "Ligero": 1.375,
    "Moderado": 1.55,
    "Intenso": 1.725,
    "Muy intenso": 1.9,
}


def normaliza(txt):
    return txt.lower().replace("á", "a").replace("é", "e").replace("í", "i").replace("ó", "o").replace("ú", "u")


def esta_prohibido(alimento, enfermedades):
    nombre = normaliza(alimento)
    grupo = DATOS_ALIMENTO.get(alimento, {}).get("grupo", "").lower()
    for enf in enfermedades:
        if enf not in PROHIBIDOS:
            continue
        for palabra in PROHIBIDOS[enf]:
            if palabra in nombre or palabra in grupo:
                return True
    return False


def clasificar_imc(imc, edad, sexo):
    """IMC adulto estándar; adolescentes 12–17: umbrales ajustados (caso clínico ~24.1 sobrepeso)."""
    if 12 <= edad <= 17:
        if imc < 18.5:
            return "Peso bajo"
        if imc < 24.0:
            return "Peso normal"
        if imc < 28.0:
            return "Sobrepeso"
        return "Obesidad"
    if edad < 12:
        if imc < 18.5:
            return "Peso bajo"
        if imc < 25.0:
            return "Peso normal"
        if imc < 30.0:
            return "Sobrepeso"
        return "Obesidad"
    if imc < 18.5:
        return "Peso bajo"
    if imc < 25:
        return "Peso normal"
    if imc < 30:
        return "Sobrepeso"
    return "Obesidad"


def categoria_etaria(edad):
    if edad < 12:
        return "Infantil"
    if edad <= 17:
        return "Adolescente"
    if edad > 60:
        return "Anciano"
    return "Adulto"


def geb_schofield_ninos_adolescentes(peso_kg, talla_cm):
    """GEB (Sheffield/Schofield caso PDF): (8.365×P) + (4.65×T) + 200 — menores de 18 años."""
    return (8.365 * peso_kg) + (4.65 * talla_cm) + 200.0


def geb_mifflin_st_jeor(peso_kg, talla_cm, edad, sexo):
    s = sexo.strip().lower()
    if s.startswith("h"):
        return 10 * peso_kg + 6.25 * talla_cm - 5 * edad + 5
    return 10 * peso_kg + 6.25 * talla_cm - 5 * edad - 161


def calcular_geb_tee(peso, altura_m, edad, sexo, actividad):
    talla_cm = altura_m * 100.0
    if edad < 18:
        geb = geb_schofield_ninos_adolescentes(peso, talla_cm)
    else:
        geb = geb_mifflin_st_jeor(peso, talla_cm, edad, sexo)
    fa = FACTOR_ACTIVIDAD.get(actividad, 1.2)
    tee = geb * fa
    return geb, tee, fa


def ajustar_kcal_objetivo(geb, tee, edad, imc_estado, objetivo):
    """
    Objetivo: bajar / mantener / subir.
    Caso PDF (adolescente hipocalórico moderado): déficit ligero sobre GEB (~99% GEB).
    Adultos: déficit/superávit sobre TEE.
    """
    if objetivo == "mantener":
        return tee if edad >= 18 else min(tee, geb * 1.05)
    if objetivo == "subir":
        return tee * 1.12 if edad >= 18 else geb * 1.08
    # bajar
    if edad < 18:
        base = geb * 0.988 if imc_estado in ("Sobrepeso", "Obesidad") else geb * 0.95
        return min(base, tee * 0.90)
    factor = 0.80 if imc_estado in ("Sobrepeso", "Obesidad") else 0.88
    return tee * factor


def distribucion_macros_tipo_pan(kcal, dislipidemia_o_cardio):
    """Distribución tipo caso clínico: 55% CHO, 20% prot, 25% grasa (ajuste leve si no dislipidemia)."""
    if dislipidemia_o_cardio:
        p_cho, p_prot, p_grasa = 0.55, 0.20, 0.25
    else:
        p_cho, p_prot, p_grasa = 0.55, 0.18, 0.27
    kcal_cho = kcal * p_cho
    kcal_prot = kcal * p_prot
    kcal_grasa = kcal * p_grasa
    g_cho = kcal_cho / 4.0
    g_prot = kcal_prot / 4.0
    g_grasa = kcal_grasa / 9.0
    return {
        "p_cho": p_cho,
        "p_prot": p_prot,
        "p_grasa": p_grasa,
        "g_cho": g_cho,
        "g_prot": g_prot,
        "g_grasa": g_grasa,
    }


def agua_ml_dia(peso_kg):
    """Rango tipo caso clínico: 30–35 ml/kg."""
    bajo = int(peso_kg * 30)
    alto = int(peso_kg * 35)
    return bajo, alto


def seleccionar_alimentos_para_comida(lista_alimentos, grupos_permitidos, kcal_objetivo, imc_estado):
    preferidos_imc_bajo = ["gallo pinto", "arroz", "huevo", "queso", "nacatamal"]
    asignados = []
    kcal_actual = 0
    if imc_estado == "Peso bajo":
        alimentos_ordenados = sorted(
            lista_alimentos,
            key=lambda x: (normaliza(x) in preferidos_imc_bajo, DATOS_ALIMENTO[x]["kcal"]),
            reverse=True,
        )
    else:
        alimentos_ordenados = sorted(
            lista_alimentos,
            key=lambda x: (DATOS_ALIMENTO[x]["grupo"] in grupos_permitidos, DATOS_ALIMENTO[x]["kcal"]),
            reverse=True,
        )
    for alimento in alimentos_ordenados:
        grupo = DATOS_ALIMENTO[alimento]["grupo"]
        kcal_alim = DATOS_ALIMENTO[alimento]["kcal"]
        if grupo in grupos_permitidos and (kcal_actual + kcal_alim) <= kcal_objetivo:
            asignados.append(f"- {alimento}: {DATOS_ALIMENTO[alimento]['porcion']} (~{kcal_alim} kcal)")
            kcal_actual += kcal_alim
    return asignados


def obs_enfermedades(flags):
    res = []
    if flags.get("diabetes"):
        res.append("- Limitar azúcares/harinas y eliminar bebidas dulces.")
    if flags.get("hipertension"):
        res.append("- Baja en sodio/sal y evitar embutidos.")
    if flags.get("obesidad"):
        res.append("- Reducir calorías, priorizar fibra, eliminar frituras y ultraprocesados.")
    if flags.get("dislipidemia"):
        res.append("- Reducir grasas saturadas y trans; priorizar cocción al horno/plancha; huevos en preparación sin exceso de grasa.")
    if flags.get("gastritis"):
        res.append("- Evitar café, frescos ácidos, picantes e irritantes; fraccionar en 5 tiempos.")
    if flags.get("anemia"):
        res.append("- Aumentar hierro y vitamina C (carnes magras, frijoles, frutas cítricas).")
    if flags.get("cardiovascular"):
        res.append("- Patrón cardioprotector: sodio controlado, grasas saturadas limitadas, actividad aeróbica regular.")
    if flags.get("endocrino"):
        res.append("- Sistema endocrino: control glucémico, regularidad de horarios, evitar azúcares simples.")
    if flags.get("renal"):
        res.append("- Riñón: hidratación según indicación médica; limitar ultraprocesados y sodio; ajuste proteico individualizado con nefrólogo.")
    return "\n".join(res)


def texto_pan_caso_tipo_pdf():
    return (
        "Nota PAN (tipo caso clínico): fraccionamiento en 5 tiempos para evitar ayunos prolongados; "
        "aumentar fibra y agua; masticación consciente; priorizar carbohidratos complejos (gallo pinto, arroz, tortilla); "
        "restringir ultraprocesados y bebidas azucaradas."
    )


def recomendar_actividad_nivel(edad, sexo, imc_estado, actividad):
    ce = categoria_etaria(edad)
    recomendaciones = []
    if ce == "Infantil":
        recomendaciones.append("Juegos activos y educación física (objetivo 60+ min/día de actividad aeróbica si es posible).")
    elif ce == "Adolescente":
        recomendaciones.append(
            "Educación física y actividad aeróbica; como en el caso clínico tipo PAN, puede aumentarse a ~60 min/día de aeróbico para perfil cardiovascular."
        )
    elif ce == "Adulto":
        recomendaciones.append("150+ min/semana de ejercicio moderado o equivalente.")
    else:
        recomendaciones.append("Actividad suave; ejercicios de equilibrio y movilidad.")
    if imc_estado == "Sobrepeso":
        recomendaciones.append("Como en el caso clínico: aumentar actividad aeróbica progresivamente; reducir sedentarismo.")
    if imc_estado == "Obesidad":
        recomendaciones.append("Ejercicio bajo guía médica si hay comorbilidades.")
    if imc_estado == "Peso bajo":
        recomendaciones.append("Evite ejercicio intenso hasta valoración; actividad ligera.")
    recomendaciones.append(f"Nivel declarado: {actividad} (factor aplicado al TEE).")
    return " ".join(recomendaciones)


def calcular_menu_semanal(kcal_sugerida, edad, sexo, imc_estado, enfermedades_marcadas, temporada):
    menu_res = []
    menu_temporada = MENU_BASE[temporada]
    for dia, tiempos in menu_temporada.items():
        resumen = [f"Día: {dia}"]
        for nombre_comida in ORDEN_COMIDAS:
            if nombre_comida not in tiempos:
                continue
            lista_alimentos = [a for a in tiempos[nombre_comida] if not esta_prohibido(a, enfermedades_marcadas)]
            kcal_objetivo = int(kcal_sugerida * PORCION_X_COMIDA[nombre_comida])
            grupos_permitidos = REGLAS_COMIDA[nombre_comida]
            seleccionados = seleccionar_alimentos_para_comida(lista_alimentos, grupos_permitidos, kcal_objetivo, imc_estado)
            if seleccionados:
                resumen.append(f"{nombre_comida.replace('_', ' ')}:")
                resumen += seleccionados
            else:
                resumen.append(f"{nombre_comida.replace('_', ' ')}: (sin alimentos aptos para las restricciones)")
        menu_res.append("\n".join(resumen))
    return "\n\n".join(menu_res)


def _ensure_data_dir():
    _DATA_DIR.mkdir(parents=True, exist_ok=True)


def cargar_json():
    _ensure_data_dir()
    if not _PACIENTES_JSON.is_file():
        return {"pacientes": [], "historial": []}
    try:
        with open(_PACIENTES_JSON, "r", encoding="utf-8") as f:
            return json.load(f)
    except (json.JSONDecodeError, OSError):
        return {"pacientes": [], "historial": []}


def guardar_json(data):
    _ensure_data_dir()
    with open(_PACIENTES_JSON, "w", encoding="utf-8") as f:
        json.dump(data, f, ensure_ascii=False, indent=2)


def _ascii_pdf_fallback(text):
    """Evita fuentes sin Unicode en PDF."""
    t = text
    for a, b in zip("áéíóúÁÉÍÓÚñÑ¿¡", "aeiouAEIOUnn??"):
        t = t.replace(a, b)
    return t


def exportar_pdf_seguro(contenido, ruta):
    if not _HAS_FPDF:
        raise RuntimeError("Instale fpdf2: pip install fpdf2")
    pdf = FPDF()
    pdf.set_auto_page_break(auto=True, margin=15)
    pdf.add_page()
    pdf.set_font("Helvetica", "", 9)
    for line in contenido.splitlines():
        line = _ascii_pdf_fallback(line)
        pdf.multi_cell(0, 5, line)
    pdf.output(ruta)


# --- GUI ---
root = tk.Tk()
root.title("Dieta Nicaragua — PAN (caso clínico + datos)")
root.geometry("1100x720")

main_container = ttk.Frame(root, padding=6)
main_container.pack(fill=tk.BOTH, expand=True)

canvas = tk.Canvas(main_container, highlightthickness=0)
scrollbar_v = ttk.Scrollbar(main_container, orient=tk.VERTICAL, command=canvas.yview)
scrollable = ttk.Frame(canvas)
scrollable.bind("<Configure>", lambda e: canvas.configure(scrollregion=canvas.bbox("all")))
canvas.create_window((0, 0), window=scrollable, anchor="nw")
canvas.configure(yscrollcommand=scrollbar_v.set)

def _on_mousewheel(event):
    canvas.yview_scroll(int(-1 * (event.delta / 120)), "units")

canvas.bind_all("<MouseWheel>", _on_mousewheel)
canvas.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
scrollbar_v.pack(side=tk.RIGHT, fill=tk.Y)

mainframe = scrollable

nombre_var = tk.StringVar()
peso_var = tk.StringVar()
altura_var = tk.StringVar()
edad_var = tk.StringVar()
sexo_var = tk.StringVar()
temporada_var = tk.StringVar(value="Verano")
actividad_var = tk.StringVar(value="Moderado")
objetivo_var = tk.StringVar(value="mantener")
alergias_med_var = tk.StringVar()
paciente_id_var = tk.StringVar()

diabetes_var = tk.BooleanVar()
hta_var = tk.BooleanVar()
obesidad_var = tk.BooleanVar()
dislipidemia_var = tk.BooleanVar()
gastritis_var = tk.BooleanVar()
anemia_var = tk.BooleanVar()
cardiovascular_var = tk.BooleanVar()
endocrino_var = tk.BooleanVar()
renal_var = tk.BooleanVar()

row = 0


def grid_label_entry(text, var, width=22):
    global row
    ttk.Label(mainframe, text=text).grid(column=0, row=row, sticky=tk.W, pady=2)
    ttk.Entry(mainframe, textvariable=var, width=width).grid(column=1, row=row, sticky=tk.W, pady=2)
    row += 1


grid_label_entry("Nombre del paciente:", nombre_var, 30)
grid_label_entry("Peso (kg):", peso_var, 12)
grid_label_entry("Altura (m) — use punto, ej. 1.55:", altura_var, 12)
grid_label_entry("Edad (años):", edad_var, 8)
grid_label_entry("Sexo (hombre/mujer):", sexo_var, 12)

ttk.Label(mainframe, text="Cargar paciente guardado:").grid(column=0, row=row, sticky=tk.W)
_frame_pac = ttk.Frame(mainframe)
_frame_pac.grid(column=1, row=row, sticky=tk.W)
paciente_combo = ttk.Combobox(_frame_pac, width=48, state="readonly")
paciente_combo.pack(side=tk.LEFT)
row += 1


def refrescar_combo_pacientes():
    data = cargar_json()
    paciente_combo["values"] = [f"{p['nombre']} | {p['id']}" for p in data.get("pacientes", [])]


def aplicar_paciente_desde_combo():
    sel = paciente_combo.get()
    if not sel or "|" not in sel:
        messagebox.showinfo("Pacientes", "No hay selección.")
        return
    pid = sel.split("|")[-1].strip()
    data = cargar_json()
    for p in data.get("pacientes", []):
        if p.get("id") != pid:
            continue
        nombre_var.set(p.get("nombre", ""))
        peso_var.set(str(p.get("peso", "")))
        altura_var.set(str(p.get("altura", "")))
        edad_var.set(str(p.get("edad", "")))
        sexo_var.set(p.get("sexo", ""))
        temporada_var.set(p.get("temporada", "Verano"))
        actividad_var.set(p.get("actividad", "Moderado"))
        objetivo_var.set(p.get("objetivo", "mantener"))
        alergias_med_var.set(p.get("alergias_medicamentos", ""))
        paciente_id_var.set(p.get("id", ""))
        fl = p.get("flags") or {}
        diabetes_var.set(fl.get("diabetes", False))
        hta_var.set(fl.get("hipertension", False))
        obesidad_var.set(fl.get("obesidad", False))
        dislipidemia_var.set(fl.get("dislipidemia", False))
        gastritis_var.set(fl.get("gastritis", False))
        anemia_var.set(fl.get("anemia", False))
        cardiovascular_var.set(fl.get("cardiovascular", False))
        endocrino_var.set(fl.get("endocrino", False))
        renal_var.set(fl.get("renal", False))
        return
    messagebox.showwarning("Pacientes", "No se encontró el registro.")


ttk.Button(_frame_pac, text="Cargar", command=aplicar_paciente_desde_combo).pack(side=tk.LEFT, padx=6)

ttk.Label(mainframe, text="Temporada:").grid(column=0, row=row, sticky=tk.W)
ttk.Combobox(
    mainframe, textvariable=temporada_var, values=["Verano", "Invierno"], state="readonly", width=14
).grid(column=1, row=row, sticky=tk.W)
row += 1

ttk.Label(mainframe, text="Nivel de actividad física:").grid(column=0, row=row, sticky=tk.W)
ttk.Combobox(
    mainframe,
    textvariable=actividad_var,
    values=list(FACTOR_ACTIVIDAD.keys()),
    state="readonly",
    width=18,
).grid(column=1, row=row, sticky=tk.W)
row += 1

ttk.Label(mainframe, text="Objetivo:").grid(column=0, row=row, sticky=tk.W)
ttk.Combobox(
    mainframe,
    textvariable=objetivo_var,
    values=["bajar", "mantener", "subir"],
    state="readonly",
    width=14,
).grid(column=1, row=row, sticky=tk.W)
row += 1

ttk.Label(mainframe, text="Alergias a medicamentos (texto libre):").grid(column=0, row=row, sticky=tk.W)
ttk.Entry(mainframe, textvariable=alergias_med_var, width=40).grid(column=1, row=row, sticky=tk.W)
row += 1

ttk.Label(mainframe, text="Enfermedades / condiciones:").grid(column=0, row=row, sticky=tk.NW)
enf_frame = ttk.Frame(mainframe)
enf_frame.grid(column=1, row=row, sticky=tk.W)
r = 0
for txt, var in [
    ("Diabetes mellitus", diabetes_var),
    ("Hipertensión arterial", hta_var),
    ("Obesidad", obesidad_var),
    ("Dislipidemia", dislipidemia_var),
    ("Gastritis / colon irritable", gastritis_var),
    ("Anemia", anemia_var),
    ("Cardiovascular (en general)", cardiovascular_var),
    ("Sistema endocrino (p. ej. tiroides, etc.)", endocrino_var),
    ("Sistema renal", renal_var),
]:
    ttk.Checkbutton(enf_frame, text=txt, variable=var).grid(column=0, row=r, sticky=tk.W)
    r += 1
row += 1

btn_frame = ttk.Frame(mainframe)
btn_frame.grid(column=0, row=row, columnspan=2, pady=8)
row += 1

resultado_text = tk.Text(mainframe, width=100, height=28, wrap="word", state="disabled")
resultado_text.grid(column=0, row=row, columnspan=2, sticky=(tk.W, tk.E), pady=6)
row += 1

ultimo_texto_resultado = {"text": ""}


def validar_entradas():
    nombre = nombre_var.get().strip()
    if not nombre:
        raise ValueError("Indique el nombre del paciente.")
    peso = float(peso_var.get().replace(",", "."))
    altura = float(altura_var.get().replace(",", "."))
    if altura <= 0:
        raise ValueError("La altura debe ser mayor que 0 (no se permite altura = 0).")
    if peso <= 0:
        raise ValueError("El peso debe ser mayor que 0.")
    if altura > 2.6:
        raise ValueError("Revise la altura en metros (ej. 1.65, no 165).")
    edad = int(float(edad_var.get().replace(",", ".")))
    if edad < 0 or edad > 120:
        raise ValueError("Edad fuera de rango.")
    sexo = sexo_var.get().strip().lower()
    if not sexo or sexo[0] not in ("h", "m"):
        raise ValueError("Sexo: escriba hombre o mujer.")
    return nombre, peso, altura, edad, sexo


def construir_flags():
    return {
        "diabetes": diabetes_var.get(),
        "hipertension": hta_var.get(),
        "obesidad": obesidad_var.get(),
        "dislipidemia": dislipidemia_var.get(),
        "gastritis": gastritis_var.get(),
        "anemia": anemia_var.get(),
        "cardiovascular": cardiovascular_var.get(),
        "endocrino": endocrino_var.get(),
        "renal": renal_var.get(),
    }


def enfermedades_lista(flags):
    m = []
    if flags["diabetes"]:
        m.append("diabetes")
    if flags["hipertension"]:
        m.append("hipertension")
    if flags["obesidad"]:
        m.append("obesidad")
    if flags["dislipidemia"]:
        m.append("dislipidemia")
    if flags["gastritis"]:
        m.append("gastritis")
    if flags["anemia"]:
        m.append("anemia")
    if flags["cardiovascular"]:
        m.append("cardiovascular")
    if flags["endocrino"]:
        m.append("endocrino")
    if flags["renal"]:
        m.append("renal")
    return m


def calcular_desde_formulario():
    try:
        nombre, peso, altura, edad, sexo = validar_entradas()
        temporada = temporada_var.get()
        actividad = actividad_var.get()
        objetivo = objetivo_var.get()
        flags = construir_flags()
        enfermedades_marcadas = enfermedades_lista(flags)

        imc = peso / (altura ** 2)
        imc_estado = clasificar_imc(imc, edad, sexo)
        cat_etaria = categoria_etaria(edad)
        geb, tee, fa = calcular_geb_tee(peso, altura, edad, sexo, actividad)

        kcal_obj = ajustar_kcal_objetivo(geb, tee, edad, imc_estado, objetivo)
        kcal_sugerida = kcal_obj
        if imc_estado == "Peso bajo":
            kcal_sugerida *= 1.10
        kcal_sugerida = max(800.0, min(5000.0, kcal_sugerida))

        dislip_o_cardio = flags["dislipidemia"] or flags["cardiovascular"]
        macros = distribucion_macros_tipo_pan(kcal_sugerida, dislip_o_cardio)
        agua_bajo, agua_alto = agua_ml_dia(peso)

        menu = calcular_menu_semanal(
            kcal_sugerida, edad, sexo, imc_estado, enfermedades_marcadas, temporada
        )
        actividad_txt = recomendar_actividad_nivel(edad, sexo, imc_estado, actividad)
        enfermedades_txt = obs_enfermedades(flags)
        alerg_txt = alergias_med_var.get().strip()

        texto = (
            f"Paciente: {nombre}\n"
            f"Fecha: {datetime.now().strftime('%Y-%m-%d %H:%M')}\n"
            f"Edad: {edad} ({cat_etaria})\n"
            f"Sexo: {sexo}\n"
            f"IMC: {imc:.2f} => {imc_estado}\n"
            f"--- Requerimiento energético (tipo caso clínico / PAN) ---\n"
            f"GEB ({'Schofield niños/adolescentes' if edad < 18 else 'Mifflin-St Jeor'}): {geb:.0f} kcal/día\n"
            f"Factor actividad ({actividad}): x{fa}\n"
            f"TEE (GEB x factor): {tee:.0f} kcal/día\n"
            f"Objetivo dietético: {objetivo}\n"
            f"Calorías prescritas (ajustadas): {int(kcal_sugerida)} kcal/día\n"
            f"Temporada menú: {temporada}\n\n"
            f"--- Distribución de macronutrientes (referencia) ---\n"
            f"CHO ~{macros['p_cho']*100:.0f}%: ~{macros['g_cho']:.1f} g | "
            f"Proteínas ~{macros['p_prot']*100:.0f}%: ~{macros['g_prot']:.1f} g | "
            f"Grasas ~{macros['p_grasa']*100:.0f}%: ~{macros['g_grasa']:.1f} g\n\n"
            f"--- Agua (orientativa, caso clínico 30–35 ml/kg) ---\n"
            f"{agua_bajo}–{agua_alto} ml/día según tolerancia e indicación médica.\n\n"
            f"--- {texto_pan_caso_tipo_pdf()} ---\n\n"
            f"--- Menú semanal (5 tiempos, alimentos típicos Nicaragua) ---\n"
            f"{menu}\n\n"
            f"--- Actividad física ---\n{actividad_txt}\n"
        )
        if enfermedades_txt:
            texto += "\n--- Observaciones por condiciones ---\n" + enfermedades_txt + "\n"
        if alerg_txt:
            texto += (
                "\n--- Alergias / intolerancias a medicamentos (registrado) ---\n"
                f"{alerg_txt}\n"
                "(Verificar siempre con prescripción; el programa no cruza fármacos con alimentos.)\n"
            )

        ultimo_texto_resultado["text"] = texto
        resultado_text.config(state="normal")
        resultado_text.delete(1.0, tk.END)
        resultado_text.insert(tk.END, texto)
        resultado_text.config(state="disabled")
    except ValueError as e:
        messagebox.showerror("Error de validación", str(e))
    except Exception as e:
        messagebox.showerror("Error", f"{type(e).__name__}: {e}")


def guardar_paciente_actual():
    try:
        nombre, peso, altura, edad, sexo = validar_entradas()
    except ValueError as e:
        messagebox.showerror("Error", str(e))
        return
    data = cargar_json()
    pid = paciente_id_var.get().strip() or str(uuid4())
    rec = {
        "id": pid,
        "nombre": nombre,
        "peso": peso,
        "altura": altura,
        "edad": edad,
        "sexo": sexo,
        "temporada": temporada_var.get(),
        "actividad": actividad_var.get(),
        "objetivo": objetivo_var.get(),
        "alergias_medicamentos": alergias_med_var.get().strip(),
        "flags": construir_flags(),
        "actualizado": datetime.now().isoformat(),
    }
    others = [p for p in data["pacientes"] if p.get("id") != pid]
    others.append(rec)
    data["pacientes"] = others
    guardar_json(data)
    paciente_id_var.set(pid)
    refrescar_combo_pacientes()
    messagebox.showinfo("Guardado", f"Paciente guardado (ID: {pid[:8]}…).")


def guardar_historial():
    if not ultimo_texto_resultado["text"]:
        messagebox.showwarning("Historial", "Calcule primero el plan.")
        return
    try:
        _, peso, altura, edad, sexo = validar_entradas()
    except ValueError as e:
        messagebox.showerror("Error", str(e))
        return
    data = cargar_json()
    pid = paciente_id_var.get().strip() or str(uuid4())
    paciente_id_var.set(pid)
    entry = {
        "id": str(uuid4()),
        "paciente_id": pid,
        "fecha": datetime.now().isoformat(),
        "peso": peso,
        "altura": altura,
        "edad": edad,
        "resumen": ultimo_texto_resultado["text"][:500],
        "plan_completo": ultimo_texto_resultado["text"],
    }
    data.setdefault("historial", []).append(entry)
    guardar_json(data)
    messagebox.showinfo("Historial", "Entrada de historial guardada.")


def exportar_pdf():
    if not ultimo_texto_resultado["text"]:
        messagebox.showwarning("PDF", "Calcule primero el plan.")
        return
    path = filedialog.asksaveasfilename(
        defaultextension=".pdf",
        filetypes=[("PDF", "*.pdf"), ("Todos", "*.*")],
        title="Guardar PDF",
    )
    if not path:
        return
    try:
        exportar_pdf_seguro(ultimo_texto_resultado["text"], path)
        messagebox.showinfo("PDF", f"Guardado:\n{path}")
    except Exception as e:
        messagebox.showerror("PDF", str(e))


ttk.Button(btn_frame, text="Calcular plan", command=calcular_desde_formulario).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Guardar datos del paciente", command=guardar_paciente_actual).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Añadir al historial de dieta", command=guardar_historial).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Exportar resultado a PDF", command=exportar_pdf).pack(side=tk.LEFT, padx=4)

ttk.Label(mainframe, text="ID paciente (opcional; se asigna al guardar):").grid(column=0, row=row, sticky=tk.W)
ttk.Entry(mainframe, textvariable=paciente_id_var, width=36).grid(column=1, row=row, sticky=tk.W)
row += 1

refrescar_combo_pacientes()

root.mainloop()
