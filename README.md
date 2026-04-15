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


# === Paleta de colores ===
_COL_VERDE_OSC = "#1B6B3A"   # verde oscuro — encabezados
_COL_VERDE_MED = "#2D8F55"   # verde medio — barra título
_COL_VERDE_CLA = "#E8F5E9"   # verde muy claro — fondo secciones
_COL_AZUL_OSC  = "#1565C0"   # azul oscuro — botón calcular
_COL_AZUL_CLA  = "#E3F2FD"   # azul claro — resaltado resultado
_COL_NARANJA   = "#E65100"   # naranja — botón PDF
_COL_GRIS_OSC  = "#424242"   # gris oscuro — texto general
_COL_BLANCO    = "#FFFFFF"
_COL_FONDO     = "#F5F5F5"   # gris muy claro — fondo ventana
_COL_BARRA     = "#EEEEEE"   # barra estado

# --- GUI ---
root = tk.Tk()
root.title("Plan Alimentario Nicaragua — PAN")
root.geometry("1160x810")
root.configure(bg=_COL_FONDO)
root.resizable(True, True)

# ── Tema ttk ─────────────────────────────────────────────
style = ttk.Style(root)
style.theme_use("clam")

style.configure(".", background=_COL_FONDO, foreground=_COL_GRIS_OSC,
                font=("Segoe UI", 10))
style.configure("TFrame",  background=_COL_FONDO)
style.configure("TLabel",  background=_COL_FONDO, foreground=_COL_GRIS_OSC)
style.configure("TCheckbutton", background=_COL_VERDE_CLA, foreground=_COL_GRIS_OSC)
style.configure("TEntry",  fieldbackground=_COL_BLANCO, relief="flat",
                borderwidth=1)
style.configure("TCombobox", fieldbackground=_COL_BLANCO, relief="flat")

# LabelFrame con borde verde
style.configure("Sec.TLabelframe",
                background=_COL_VERDE_CLA,
                relief="groove",
                bordercolor=_COL_VERDE_MED,
                borderwidth=2)
style.configure("Sec.TLabelframe.Label",
                background=_COL_VERDE_CLA,
                foreground=_COL_VERDE_OSC,
                font=("Segoe UI", 10, "bold"))

# Botones con colores diferenciados
style.configure("Calcular.TButton",
                background=_COL_AZUL_OSC, foreground=_COL_BLANCO,
                font=("Segoe UI", 10, "bold"),
                padding=(12, 6),
                relief="flat")
style.map("Calcular.TButton",
          background=[("active", "#1976D2"), ("pressed", "#0D47A1")])

style.configure("Guardar.TButton",
                background=_COL_VERDE_MED, foreground=_COL_BLANCO,
                font=("Segoe UI", 10),
                padding=(10, 6),
                relief="flat")
style.map("Guardar.TButton",
          background=[("active", "#388E3C"), ("pressed", "#1B6B3A")])

style.configure("Historial.TButton",
                background="#6A1B9A", foreground=_COL_BLANCO,
                font=("Segoe UI", 10),
                padding=(10, 6),
                relief="flat")
style.map("Historial.TButton",
          background=[("active", "#7B1FA2"), ("pressed", "#4A148C")])

style.configure("PDF.TButton",
                background=_COL_NARANJA, foreground=_COL_BLANCO,
                font=("Segoe UI", 10),
                padding=(10, 6),
                relief="flat")
style.map("PDF.TButton",
          background=[("active", "#F4511E"), ("pressed", "#BF360C")])

style.configure("Cargar.TButton",
                background="#546E7A", foreground=_COL_BLANCO,
                font=("Segoe UI", 9),
                padding=(6, 4),
                relief="flat")
style.map("Cargar.TButton",
          background=[("active", "#607D8B"), ("pressed", "#37474F")])

# ── Encabezado (banner) ───────────────────────────────────
header_frame = tk.Frame(root, bg=_COL_VERDE_MED, height=60)
header_frame.pack(fill=tk.X, side=tk.TOP)
header_frame.pack_propagate(False)
tk.Label(
    header_frame,
    text="🥗  Plan Alimentario Nicaragua — PAN",
    bg=_COL_VERDE_MED, fg=_COL_BLANCO,
    font=("Segoe UI", 16, "bold"),
    anchor="w", padx=16,
).pack(fill=tk.BOTH, expand=True)

# ── Contenedor principal con scroll ──────────────────────
main_container = ttk.Frame(root, padding=0)
main_container.pack(fill=tk.BOTH, expand=True)

canvas = tk.Canvas(main_container, highlightthickness=0, bg=_COL_FONDO)
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

# ── Variables del formulario ──────────────────────────────
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

# ── Barra de estado (status bar) — definida antes de usarla ─
status_var = tk.StringVar(value="Listo.")
status_bar = tk.Label(
    root,
    textvariable=status_var,
    bg=_COL_BARRA, fg=_COL_GRIS_OSC,
    font=("Segoe UI", 9),
    anchor="w", padx=10, relief="sunken",
)
status_bar.pack(fill=tk.X, side=tk.BOTTOM)


def _set_status(msg, color=_COL_GRIS_OSC):
    status_var.set(msg)
    status_bar.configure(fg=color)


# ── Fila 0: sección superior — dos columnas ───────────────
top_frame = ttk.Frame(mainframe, padding=(10, 10, 10, 4))
top_frame.grid(column=0, row=0, columnspan=2, sticky=(tk.W, tk.E))
top_frame.columnconfigure(0, weight=1)
top_frame.columnconfigure(1, weight=1)

# ── LabelFrame: Datos del paciente ───────────────────────
lf_datos = ttk.LabelFrame(top_frame, text=" Datos del paciente ", style="Sec.TLabelframe",
                           padding=(12, 8))
lf_datos.grid(column=0, row=0, sticky=(tk.N, tk.W, tk.E), padx=(0, 8))

_PAD = {"padx": 4, "pady": 3}


def _lbl_entry(parent, text, var, width=22, row_n=0):
    ttk.Label(parent, text=text, background=_COL_VERDE_CLA).grid(
        column=0, row=row_n, sticky=tk.W, **_PAD)
    ttk.Entry(parent, textvariable=var, width=width).grid(
        column=1, row=row_n, sticky=tk.W, **_PAD)


_lbl_entry(lf_datos, "Nombre del paciente:", nombre_var, 28, 0)
_lbl_entry(lf_datos, "Peso (kg):", peso_var, 12, 1)
_lbl_entry(lf_datos, "Altura (m)  ej. 1.55:", altura_var, 12, 2)
_lbl_entry(lf_datos, "Edad (años):", edad_var, 8, 3)
_lbl_entry(lf_datos, "Sexo (hombre / mujer):", sexo_var, 12, 4)

# Cargar paciente guardado
ttk.Label(lf_datos, text="Cargar paciente guardado:", background=_COL_VERDE_CLA).grid(
    column=0, row=5, sticky=tk.W, **_PAD)
_frame_pac = ttk.Frame(lf_datos, style="Sec.TLabelframe")
_frame_pac.grid(column=1, row=5, sticky=tk.W, **_PAD)
paciente_combo = ttk.Combobox(_frame_pac, width=30, state="readonly")
paciente_combo.pack(side=tk.LEFT)

ttk.Label(lf_datos, text="ID paciente (asignado al guardar):", background=_COL_VERDE_CLA).grid(
    column=0, row=6, sticky=tk.W, **_PAD)
ttk.Entry(lf_datos, textvariable=paciente_id_var, width=28).grid(
    column=1, row=6, sticky=tk.W, **_PAD)

# ── LabelFrame: Configuración del plan ───────────────────
lf_conf = ttk.LabelFrame(top_frame, text=" Configuración del plan ", style="Sec.TLabelframe",
                          padding=(12, 8))
lf_conf.grid(column=1, row=0, sticky=(tk.N, tk.W, tk.E))

ttk.Label(lf_conf, text="Temporada:", background=_COL_VERDE_CLA).grid(
    column=0, row=0, sticky=tk.W, **_PAD)
ttk.Combobox(lf_conf, textvariable=temporada_var,
             values=["Verano", "Invierno"], state="readonly", width=16).grid(
    column=1, row=0, sticky=tk.W, **_PAD)

ttk.Label(lf_conf, text="Actividad física:", background=_COL_VERDE_CLA).grid(
    column=0, row=1, sticky=tk.W, **_PAD)
ttk.Combobox(lf_conf, textvariable=actividad_var,
             values=list(FACTOR_ACTIVIDAD.keys()), state="readonly", width=16).grid(
    column=1, row=1, sticky=tk.W, **_PAD)

ttk.Label(lf_conf, text="Objetivo:", background=_COL_VERDE_CLA).grid(
    column=0, row=2, sticky=tk.W, **_PAD)
ttk.Combobox(lf_conf, textvariable=objetivo_var,
             values=["bajar", "mantener", "subir"], state="readonly", width=16).grid(
    column=1, row=2, sticky=tk.W, **_PAD)

ttk.Label(lf_conf, text="Alergias / medicamentos:", background=_COL_VERDE_CLA).grid(
    column=0, row=3, sticky=tk.W, **_PAD)
ttk.Entry(lf_conf, textvariable=alergias_med_var, width=28).grid(
    column=1, row=3, sticky=tk.W, **_PAD)

# ── LabelFrame: Enfermedades / condiciones ────────────────
lf_enf = ttk.LabelFrame(top_frame, text=" Enfermedades / condiciones ", style="Sec.TLabelframe",
                         padding=(12, 8))
lf_enf.grid(column=1, row=1, sticky=(tk.N, tk.W, tk.E), pady=(8, 0))

_ENF_ITEMS = [
    ("Diabetes mellitus", diabetes_var),
    ("Hipertensión arterial", hta_var),
    ("Obesidad", obesidad_var),
    ("Dislipidemia", dislipidemia_var),
    ("Gastritis / colon irritable", gastritis_var),
    ("Anemia", anemia_var),
    ("Cardiovascular (en general)", cardiovascular_var),
    ("Sistema endocrino (tiroides, etc.)", endocrino_var),
    ("Sistema renal", renal_var),
]
for _r, (_txt, _var) in enumerate(_ENF_ITEMS):
    ttk.Checkbutton(lf_enf, text=_txt, variable=_var).grid(
        column=0, row=_r, sticky=tk.W, pady=1)

# ── Botones ───────────────────────────────────────────────
btn_frame = ttk.Frame(mainframe, padding=(10, 6))
btn_frame.grid(column=0, row=1, columnspan=2, sticky=(tk.W, tk.E))

_btn_calcular  = ttk.Button(btn_frame, text="▶  Calcular plan",  style="Calcular.TButton")
_btn_guardar   = ttk.Button(btn_frame, text="💾  Guardar paciente", style="Guardar.TButton")
_btn_historial = ttk.Button(btn_frame, text="📋  Añadir al historial", style="Historial.TButton")
_btn_pdf       = ttk.Button(btn_frame, text="📄  Exportar PDF",    style="PDF.TButton")
for _b in (_btn_calcular, _btn_guardar, _btn_historial, _btn_pdf):
    _b.pack(side=tk.LEFT, padx=(0, 8))

# ── Área de resultado ─────────────────────────────────────
res_frame = ttk.LabelFrame(mainframe, text=" Resultado del plan ", style="Sec.TLabelframe",
                            padding=(8, 6))
res_frame.grid(column=0, row=2, columnspan=2, sticky=(tk.W, tk.E), padx=10, pady=(0, 6))
res_frame.columnconfigure(0, weight=1)

resultado_text = tk.Text(
    res_frame,
    width=110, height=26,
    wrap="word",
    state="disabled",
    font=("Consolas", 9),
    bg="#FAFFFE",
    fg=_COL_GRIS_OSC,
    relief="flat",
    borderwidth=1,
    selectbackground=_COL_AZUL_CLA,
)
_scroll_res = ttk.Scrollbar(res_frame, orient=tk.VERTICAL,
                             command=resultado_text.yview)
resultado_text.configure(yscrollcommand=_scroll_res.set)
resultado_text.grid(column=0, row=0, sticky=(tk.W, tk.E))
_scroll_res.grid(column=1, row=0, sticky=tk.NS)

# Tags de color para secciones del resultado
resultado_text.tag_configure("titulo",
                              font=("Consolas", 10, "bold"),
                              foreground=_COL_VERDE_OSC)
resultado_text.tag_configure("seccion",
                              font=("Consolas", 9, "bold"),
                              foreground=_COL_AZUL_OSC)
resultado_text.tag_configure("advertencia",
                              foreground=_COL_NARANJA,
                              font=("Consolas", 9, "bold"))
resultado_text.tag_configure("normal",
                              foreground=_COL_GRIS_OSC)

ultimo_texto_resultado = {"text": ""}


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
        _set_status(f"Paciente cargado: {p.get('nombre', '')}", _COL_VERDE_OSC)
        return
    messagebox.showwarning("Pacientes", "No se encontró el registro.")


ttk.Button(_frame_pac, text="Cargar", style="Cargar.TButton",
           command=aplicar_paciente_desde_combo).pack(side=tk.LEFT, padx=6)


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


def _insertar_con_tags(text_widget, texto):
    """Inserta el texto con etiquetas de color por sección."""
    text_widget.config(state="normal")
    text_widget.delete(1.0, tk.END)
    for linea in texto.splitlines(keepends=True):
        stripped = linea.strip()
        if stripped.startswith("Paciente:") or stripped.startswith("====="):
            text_widget.insert(tk.END, linea, "titulo")
        elif stripped.startswith("---") and stripped.endswith("---"):
            text_widget.insert(tk.END, linea, "seccion")
        elif stripped.startswith("Día:"):
            text_widget.insert(tk.END, linea, "seccion")
        elif stripped.startswith(("- Limitar", "- Reducir", "- Evitar", "- Baja")):
            text_widget.insert(tk.END, linea, "advertencia")
        else:
            text_widget.insert(tk.END, linea, "normal")
    text_widget.config(state="disabled")


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
        _insertar_con_tags(resultado_text, texto)
        _set_status(
            f"Plan calculado para {nombre}  |  IMC {imc:.2f} ({imc_estado})  |  {int(kcal_sugerida)} kcal/día",
            _COL_VERDE_OSC,
        )
    except ValueError as e:
        messagebox.showerror("Error de validación", str(e))
        _set_status(f"Error: {e}", _COL_NARANJA)
    except Exception as e:
        messagebox.showerror("Error", f"{type(e).__name__}: {e}")
        _set_status(f"Error inesperado: {e}", _COL_NARANJA)


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
    _set_status(f"Paciente guardado: {nombre}  (ID: {pid[:8]}…)", _COL_VERDE_OSC)
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
    _set_status("Entrada de historial guardada.", _COL_VERDE_OSC)
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
        _set_status(f"PDF exportado: {path}", _COL_VERDE_OSC)
        messagebox.showinfo("PDF", f"Guardado:\n{path}")
    except Exception as e:
        messagebox.showerror("PDF", str(e))
        _set_status(f"Error al exportar PDF: {e}", _COL_NARANJA)


# Assign commands to explicitly stored button references
_btn_calcular.configure(command=calcular_desde_formulario)
_btn_guardar.configure(command=guardar_paciente_actual)
_btn_historial.configure(command=guardar_historial)
_btn_pdf.configure(command=exportar_pdf)

# Acceso rápido: Ctrl+Enter para calcular
root.bind("<Control-Return>", lambda _e: calcular_desde_formulario())

refrescar_combo_pacientes()

root.mainloop()
