    if flags["dislipidemia"]:

def enfermedades_lista(flags):
        "anemia": "anemia" in seleccion,

        raise ValueError("Edad fuera de rango.")
    if altura <= 0:
        raise ValueError("La altura debe ser mayor que 0 (no se permite altura = 0).")
    if peso <= 0:
resultado_text.grid(column=0, row=2, columnspan=2, sticky=(tk.W, tk.E), pady=(0, 8), padx=8)

ultimo_texto_resultado = {"text": ""}


def validar_entradas():
        return
    messagebox.showwarning("Pacientes", "No se encontró el registro.")
        alergias_med_var.set(p.get("alergias_medicamentos", ""))
        paciente_id_var.set(p.get("id", ""))
        fl = p.get("flags") or {}
        peso_var.set(str(p.get("peso", "")))
        altura_var.set(str(p.get("altura", "")))
    pid = sel.split("|")[-1].strip()


_actualizar_lista_enfermedades()


        enfermedades_listbox.insert(tk.END, txt)
    enfermedades_listbox.delete(0, tk.END)
    etiquetas = [txt for key, txt in ENFERMEDADES_OPCIONES if key in enfermedades_seleccionadas]
    resumen_enf_var.set("Seleccionadas: " + ", ".join(etiquetas))

scroll_enf.pack(side=tk.RIGHT, fill=tk.Y)
    height=8,

paciente_combo.pack(fill=tk.X, pady=(4, 0))
_crear_combo(card_izq, "Objetivo:", objetivo_var, ["bajar", "mantener", "subir"], 16)
_crear_campo(card_izq, "[KG] Peso (kg):", peso_var, 14)
radios_sexo = tk.Frame(frame_sexo, bg="white")


    tk.Label(frame, text=text, bg="white", fg="#374151", font=("Segoe UI", 10)).pack(anchor="w")
    ttk.Entry(frame, textvariable=var, width=width).pack(fill=tk.X, pady=(4, 0))

tk.Label(card_izq, text="Datos del paciente", bg="white", fg="#1f2937", font=("Segoe UI", 12, "bold")).pack(anchor="w", pady=(0, 15))

objetivo_var = tk.StringVar(value="mantener")
alergias_med_var = tk.StringVar()

canvas.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)
scrollbar_v.pack(side=tk.RIGHT, fill=tk.Y)
canvas.configure(bg="#eef1f4")
main_container.pack(fill=tk.BOTH, expand=True)

canvas = tk.Canvas(main_container, highlightthickness=0)
root = tk.Tk()
        line = _ascii_pdf_fallback(line)
        raise RuntimeError("Instale fpdf2: pip install fpdf2")
    for a, b in zip("áéíóúÁÉÍÓÚñÑ¿¡", "aeiouAEIOUnn??"):
        t = t.replace(a, b)
        json.dump(data, f, ensure_ascii=False, indent=2)
    except (json.JSONDecodeError, OSError):
        return {"pacientes": [], "historial": []}

def cargar_json():

                resumen.append(f"{nombre_comida.replace('_', ' ')}:")
        for nombre_comida in ORDEN_COMIDAS:
            if nombre_comida not in tiempos:
                continue

    if imc_estado == "Obesidad":
        recomendaciones.append("Ejercicio bajo guía médica si hay comorbilidades.")
        )
    elif ce == "Adulto":
    ce = categoria_etaria(edad)
    recomendaciones = []
        "aumentar fibra y agua; masticación consciente; priorizar carbohidratos complejos (gallo pinto, arroz, tortilla); "
        res.append("- Riñón: hidratación según indicación médica; limitar ultraprocesados y sodio; ajuste proteico individualizado con nefrólogo.")
    return "\n".join(res)
    if flags.get("anemia"):
        res.append("- Aumentar hierro y vitamina C (carnes magras, frijoles, frutas cítricas).")
        res.append("- Baja en sodio/sal y evitar embutidos.")
    if flags.get("obesidad"):

        )
    for alimento in alimentos_ordenados:
            reverse=True,
        )
    asignados = []
    alto = int(peso_kg * 35)
    }
        "p_cho": p_cho,
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
    fa = FACTOR_ACTIVIDAD.get(actividad, 1.2)


def calcular_geb_tee(peso, altura_m, edad, sexo, actividad):


    if edad < 12:
        return "Infantil"
        return "Peso normal"
    if imc < 30:
        if imc < 30.0:
            return "Sobrepeso"
        return "Obesidad"
    if 12 <= edad <= 17:
                return True
    nombre = normaliza(alimento)
    grupo = DATOS_ALIMENTO.get(alimento, {}).get("grupo", "").lower()
    ("renal", "Sistema renal"),
]
    ("obesidad", "Obesidad"),
    "Muy intenso": 1.9,
    "Cena": 0.25,
}
    "Cena": ["proteina", "verdura", "fruta"],
}


ORDEN_COMIDAS = ["Desayuno", "Merienda_AM", "Almuerzo", "Merienda_PM", "Cena"]
    "cardiovascular": {"queso", "sal", "embutido", "fritura", "grasa"},
PROHIBIDOS = {
    "diabetes": {"bebida", "azucar", "fresco", "tiste", "chicha", "cacao"},
    "Mango": {"kcal": 70, "porcion": "1 unidad (120g)", "grupo": "fruta"},
    "fresco de cacao": {"kcal": 100, "porcion": "1 vaso (200ml)", "grupo": "bebida"},
    "Piña": {"kcal": 55, "porcion": "1 taza (140g)", "grupo": "fruta"},
    "Arroz": {"kcal": 110, "porcion": "1/2 taza (65g)", "grupo": "carbohidrato"},
    "Huevo frito": {"kcal": 90, "porcion": "1 unidad (50g)", "grupo": "proteina"},
    },
            "Merienda_PM": ["Naranja", "Melón", "Banano"],
            "Merienda_AM": ["Mango", "Naranja", "Banano"],
            "Almuerzo": ["Sopa de gallina", "Ensalada de repollo", "Naranja", "Piña"],
            "Merienda_PM": ["Papaya", "Sandía", "Melón"],
            "Cena": ["Queso", "Tortilla", "Naranja"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Mango"],
            "Merienda_AM": ["Mango", "Sandía", "Papaya"],
            "Almuerzo": ["Sopa de gallina", "Arroz", "Ensalada", "Naranja"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Mango"],
# -*- coding: utf-8 -*-
"""
Plan alimentario Nicaragua — PAN inspirado en caso clínico (Schofield/GEB, 5 tiempos,
ajustes por actividad, objetivo y patologías). No sustituye valoración profesional.
"""
import json
import importlib
import re
import tkinter as tk
from datetime import datetime
from pathlib import Path
from tkinter import filedialog, messagebox, ttk
from uuid import uuid4

try:
    FPDF = importlib.import_module("fpdf").FPDF
    _HAS_FPDF = True
except Exception:
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
            "Merienda_PM": ["Piña", "Melón", "Sandía"],
            "Cena": ["Queso", "Tortilla", "Papaya"],
        },
        "Martes": {
            "Desayuno": ["Güirila con cuajada", "Tiste"],
            "Merienda_AM": ["Naranja", "Banano", "Papaya"],
            "Merienda_PM": ["Sandía", "Melón", "Piña"],
            "Cena": ["Queso", "Tortilla", "Melón"],
        },
        "Miércoles": {
            "Desayuno": ["Gallo pinto seco", "Queso", "Tortilla", "Café con leche"],
            "Almuerzo": ["Sopa de frijoles", "Arroz", "Ensalada", "Papaya"],
            "Merienda_PM": ["Naranja", "Piña", "Banano"],
            "Cena": ["Queso", "Tortilla", "Piña"],
        },
        "Jueves": {
            "Desayuno": ["Elote cocido", "Café con leche"],
            "Merienda_AM": ["Papaya", "Naranja", "Melón"],
            "Merienda_PM": ["Sandía", "Piña", "Papaya"],
            "Cena": ["Queso", "Tortilla", "Melón"],
        },
        "Viernes": {
            "Desayuno": ["Gallo pinto", "Queso", "Tortilla", "Tiste"],
        },
        "Sábado": {
            "Desayuno": ["Nacatamal (fines de semana)", "Tiste"],
            "Merienda_AM": ["Piña", "Sandía", "Papaya"],
            "Almuerzo": ["Sopa de res", "Arroz", "Ensalada", "Papaya"],
            "Cena": ["Queso", "Tortilla", "Sandía"],
        },
        "Domingo": {
            "Desayuno": ["Gallo pinto", "Huevo frito", "Tortilla", "Café con leche"],
            "Merienda_AM": ["Mango", "Papaya", "Sandía"],
            "Almuerzo": ["Sopa de gallina", "Ensalada de repollo", "Mango", "Chicha de maíz"],
            "Merienda_PM": ["Piña", "Naranja", "Melón"],
            "Cena": ["Queso", "Tortilla", "Piña"],
        },
}

DATOS_ALIMENTO = {
    "Gallo pinto": {"kcal": 130, "porcion": "1/2 taza (120g)", "grupo": "carbohidrato"},
    "Gallo pinto seco": {"kcal": 120, "porcion": "1/2 taza (120g)", "grupo": "carbohidrato"},
    "Queso": {"kcal": 80, "porcion": "1 rebanada (30g)", "grupo": "proteina"},
    "Tortilla": {"kcal": 70, "porcion": "1 unidad (40g)", "grupo": "carbohidrato"},
    "Café con leche": {"kcal": 70, "porcion": "1 taza (240ml)", "grupo": "bebida"},
    "Tiste": {"kcal": 120, "porcion": "1 taza (240ml)", "grupo": "bebida"},
    "Pescado": {"kcal": 120, "porcion": "1 filete (80g)", "grupo": "proteina"},
    "Ensalada": {"kcal": 40, "porcion": "1 taza (80g)", "grupo": "verdura"},
    "Ensaladas": {"kcal": 35, "porcion": "1 taza (60g)", "grupo": "verdura"},
    "Ensalada de repollo": {"kcal": 40, "porcion": "1/2 taza (70g)", "grupo": "verdura"},
    "Sandía": {"kcal": 30, "porcion": "1 rodaja (150g)", "grupo": "fruta"},
    "Melón": {"kcal": 60, "porcion": "1 rodaja (150g)", "grupo": "fruta"},
    "Naranja": {"kcal": 60, "porcion": "1 mediana (130g)", "grupo": "fruta"},
    "Papaya": {"kcal": 55, "porcion": "1 taza (140g)", "grupo": "fruta"},
    "Banano": {"kcal": 90, "porcion": "1 unidad mediana (100g)", "grupo": "fruta"},
    "Vigorón": {"kcal": 300, "porcion": "1 porción (250g)", "grupo": "carbohidrato"},
    "Chicha de maíz": {"kcal": 100, "porcion": "1 vaso (250ml)", "grupo": "bebida"},
    "fresco de frutas naturales": {"kcal": 90, "porcion": "1 vaso (200ml)", "grupo": "bebida"},
    "Nacatamal (fines de semana)": {"kcal": 450, "porcion": "1 unidad (300g)", "grupo": "carbohidrato"},
    "Sopa de res": {"kcal": 150, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Sopa de gallina": {"kcal": 120, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Sopa de frijoles": {"kcal": 110, "porcion": "1 taza (250ml)", "grupo": "proteina"},
    "Elote cocido": {"kcal": 70, "porcion": "1 mazorca (90g)", "grupo": "carbohidrato"},
    "Güirila con cuajada": {"kcal": 180, "porcion": "1 unidad (100g)", "grupo": "carbohidrato"},
}

# Palabras clave por condición (filtrado de alimentos del menú)
    "hipertension": {"queso", "sal", "fresco", "embutido"},
    "obesidad": {"fritura", "grasa", "nacatamal", "vigoron"},
    "dislipidemia": {"fritura", "grasa", "vigoron", "nacatamal"},
    "gastritis": {"cafe", "picante", "alcohol", "fresco", "tiste", "chicha"},
    "anemia": set(),
    "endocrino": {"bebida", "azucar", "fresco", "tiste", "chicha", "cacao", "gaseosa"},
    # Renal: evitar palabras genéricas como "fresco" (aparece en nombres de fruta/bebida)
    "renal": {"sal", "embutido", "chicha", "cacao"},
}

REGLAS_COMIDA = {
    "Desayuno": ["carbohidrato", "proteina"],
    "Merienda_AM": ["fruta", "verdura", "bebida"],
    "Almuerzo": ["proteina", "carbohidrato", "verdura"],
    "Merienda_PM": ["fruta", "verdura", "bebida"],
PORCION_X_COMIDA = {
    "Desayuno": 0.20,
    "Merienda_AM": 0.10,
    "Almuerzo": 0.35,
    "Merienda_PM": 0.10,

FACTOR_ACTIVIDAD = {
    "Sedentario": 1.2,
    "Ligero": 1.375,
    "Moderado": 1.55,
    "Intenso": 1.725,
}

ENFERMEDADES_OPCIONES = [
    ("diabetes", "Diabetes mellitus"),
    ("hipertension", "Hipertension arterial"),
    ("dislipidemia", "Dislipidemia"),
    ("gastritis", "Gastritis / colon irritable"),
    ("anemia", "Anemia"),
    ("cardiovascular", "Cardiovascular (en general)"),
    ("endocrino", "Sistema endocrino (p. ej. tiroides, etc.)"),


def normaliza(txt):
    return txt.lower().replace("á", "a").replace("é", "e").replace("í", "i").replace("ó", "o").replace("ú", "u")


def esta_prohibido(alimento, enfermedades):
    for enf in enfermedades:
        if enf not in PROHIBIDOS:
            continue
        for palabra in PROHIBIDOS[enf]:
            if palabra in nombre or palabra in grupo:
    return False


def clasificar_imc(imc, edad, sexo):
    """IMC adulto estándar; adolescentes 12–17: umbrales ajustados (caso clínico ~24.1 sobrepeso)."""
        if imc < 18.5:
            return "Peso bajo"
        if imc < 24.0:
            return "Peso normal"
        if imc < 28.0:
    if edad < 12:
        if imc < 18.5:
            return "Peso bajo"
        if imc < 25.0:
            return "Peso normal"
            return "Sobrepeso"
        return "Obesidad"
    if imc < 18.5:
        return "Peso bajo"
    if imc < 25:
        return "Sobrepeso"
    return "Obesidad"


def categoria_etaria(edad):
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
    talla_cm = altura_m * 100.0
    if edad < 18:
        geb = geb_schofield_ninos_adolescentes(peso, talla_cm)
    else:
        geb = geb_mifflin_st_jeor(peso, talla_cm, edad, sexo)
    tee = geb * fa
    return geb, tee, fa


def ajustar_kcal_objetivo(geb, tee, edad, imc_estado, objetivo):
    """
    kcal_cho = kcal * p_cho
    kcal_prot = kcal * p_prot
    kcal_grasa = kcal * p_grasa
    g_cho = kcal_cho / 4.0
    g_prot = kcal_prot / 4.0
    g_grasa = kcal_grasa / 9.0
    return {
        "p_prot": p_prot,
        "p_grasa": p_grasa,
        "g_cho": g_cho,
        "g_prot": g_prot,
        "g_grasa": g_grasa,


def agua_ml_dia(peso_kg):
    """Rango tipo caso clínico: 30–35 ml/kg."""
    bajo = int(peso_kg * 30)
    return bajo, alto


def seleccionar_alimentos_para_comida(lista_alimentos, grupos_permitidos, kcal_objetivo, imc_estado):
    preferidos_imc_bajo = ["gallo pinto", "arroz", "huevo", "queso", "nacatamal"]
    kcal_actual = 0
    if imc_estado == "Peso bajo":
        alimentos_ordenados = sorted(
            lista_alimentos,
            key=lambda x: (normaliza(x) in preferidos_imc_bajo, DATOS_ALIMENTO[x]["kcal"]),
    else:
        alimentos_ordenados = sorted(
            lista_alimentos,
            key=lambda x: (DATOS_ALIMENTO[x]["grupo"] in grupos_permitidos, DATOS_ALIMENTO[x]["kcal"]),
            reverse=True,
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
        res.append("- Reducir calorías, priorizar fibra, eliminar frituras y ultraprocesados.")
    if flags.get("dislipidemia"):
        res.append("- Reducir grasas saturadas y trans; priorizar cocción al horno/plancha; huevos en preparación sin exceso de grasa.")
    if flags.get("gastritis"):
        res.append("- Evitar café, frescos ácidos, picantes e irritantes; fraccionar en 5 tiempos.")
    if flags.get("cardiovascular"):
        res.append("- Patrón cardioprotector: sodio controlado, grasas saturadas limitadas, actividad aeróbica regular.")
    if flags.get("endocrino"):
        res.append("- Sistema endocrino: control glucémico, regularidad de horarios, evitar azúcares simples.")
    if flags.get("renal"):


def texto_pan_caso_tipo_pdf():
    return (
        "Nota PAN (tipo caso clínico): fraccionamiento en 5 tiempos para evitar ayunos prolongados; "
        "restringir ultraprocesados y bebidas azucaradas."
    )


def recomendar_actividad_nivel(edad, sexo, imc_estado, actividad):
    if ce == "Infantil":
        recomendaciones.append("Juegos activos y educación física (objetivo 60+ min/día de actividad aeróbica si es posible).")
    elif ce == "Adolescente":
        recomendaciones.append(
            "Educación física y actividad aeróbica; como en el caso clínico tipo PAN, puede aumentarse a ~60 min/día de aeróbico para perfil cardiovascular."
        recomendaciones.append("150+ min/semana de ejercicio moderado o equivalente.")
    else:
        recomendaciones.append("Actividad suave; ejercicios de equilibrio y movilidad.")
    if imc_estado == "Sobrepeso":
        recomendaciones.append("Como en el caso clínico: aumentar actividad aeróbica progresivamente; reducir sedentarismo.")
    if imc_estado == "Peso bajo":
        recomendaciones.append("Evite ejercicio intenso hasta valoración; actividad ligera.")
    recomendaciones.append(f"Nivel declarado: {actividad} (factor aplicado al TEE).")
    return " ".join(recomendaciones)

def calcular_menu_semanal(kcal_sugerida, edad, sexo, imc_estado, enfermedades_marcadas, temporada):
    menu_res = []
    menu_temporada = MENU_BASE[temporada]
    for dia, tiempos in menu_temporada.items():
        resumen = [f"Día: {dia}"]
            lista_alimentos = [a for a in tiempos[nombre_comida] if not esta_prohibido(a, enfermedades_marcadas)]
            kcal_objetivo = int(kcal_sugerida * PORCION_X_COMIDA[nombre_comida])
            grupos_permitidos = REGLAS_COMIDA[nombre_comida]
            seleccionados = seleccionar_alimentos_para_comida(lista_alimentos, grupos_permitidos, kcal_objetivo, imc_estado)
            if seleccionados:
                resumen += seleccionados
            else:
                resumen.append(f"{nombre_comida.replace('_', ' ')}: (sin alimentos aptos para las restricciones)")
        menu_res.append("\n".join(resumen))
    return "\n\n".join(menu_res)

def _ensure_data_dir():
    _DATA_DIR.mkdir(parents=True, exist_ok=True)

    _ensure_data_dir()
    if not _PACIENTES_JSON.is_file():
        return {"pacientes": [], "historial": []}
    try:
        with open(_PACIENTES_JSON, "r", encoding="utf-8") as f:
            return json.load(f)


def guardar_json(data):
    _ensure_data_dir()
    with open(_PACIENTES_JSON, "w", encoding="utf-8") as f:


def _ascii_pdf_fallback(text):
    """Evita fuentes sin Unicode en PDF."""
    t = text
    return t


def exportar_pdf_seguro(contenido, ruta):
    if not _HAS_FPDF:
    pdf = FPDF()
    pdf.set_auto_page_break(auto=True, margin=15)
    pdf.add_page()
    pdf.set_font("Helvetica", "", 9)
    for line in contenido.splitlines():
        pdf.multi_cell(0, 5, line)
    pdf.output(ruta)


# --- GUI ---
root.title("Dieta Nicaragua — PAN (caso clínico + datos)")
root.geometry("1100x720")
root.configure(bg="#eef1f4")

main_container = ttk.Frame(root, padding=12)
scrollbar_v = ttk.Scrollbar(main_container, orient=tk.VERTICAL, command=canvas.yview)
scrollable = ttk.Frame(canvas, padding=4)
scrollable.bind("<Configure>", lambda e: canvas.configure(scrollregion=canvas.bbox("all")))
canvas.create_window((0, 0), window=scrollable, anchor="nw")
canvas.configure(yscrollcommand=scrollbar_v.set)

def _on_mousewheel(event):
    canvas.yview_scroll(int(-1 * (event.delta / 120)), "units")

canvas.bind_all("<MouseWheel>", _on_mousewheel)

mainframe = scrollable
mainframe.columnconfigure(0, weight=1)
mainframe.columnconfigure(1, weight=1)
nombre_var = tk.StringVar()
peso_var = tk.StringVar()
altura_var = tk.StringVar()
edad_var = tk.StringVar()
sexo_var = tk.StringVar(value="hombre")
temporada_var = tk.StringVar(value="Verano")
actividad_var = tk.StringVar(value="Moderado")
paciente_id_var = tk.StringVar()
buscar_enf_var = tk.StringVar()
resumen_enf_var = tk.StringVar(value="Sin enfermedades seleccionadas")

enfermedades_seleccionadas = set()
enfermedades_filtradas = []
card_izq = tk.Frame(mainframe, bg="white", padx=18, pady=18, highlightbackground="#d9dde3", highlightthickness=1)
card_der = tk.Frame(mainframe, bg="white", padx=18, pady=18, highlightbackground="#d9dde3", highlightthickness=1)
card_izq.grid(column=0, row=0, sticky=(tk.N, tk.E, tk.W), padx=8, pady=8)
card_der.grid(column=1, row=0, sticky=(tk.N, tk.E, tk.W), padx=8, pady=8)
tk.Label(card_der, text="Condiciones y alergias", bg="white", fg="#1f2937", font=("Segoe UI", 12, "bold")).pack(anchor="w", pady=(0, 15))


def _crear_campo(parent, text, var, width=26):
    frame = tk.Frame(parent, bg="white")
    frame.pack(fill=tk.X, pady=(0, 15))


def _crear_combo(parent, text, var, values, width=24):
    frame = tk.Frame(parent, bg="white")
    frame.pack(fill=tk.X, pady=(0, 15))
    tk.Label(frame, text=text, bg="white", fg="#374151", font=("Segoe UI", 10)).pack(anchor="w")
    ttk.Combobox(frame, textvariable=var, values=values, state="readonly", width=width).pack(fill=tk.X, pady=(4, 0))
_crear_campo(card_izq, "Nombre del paciente:", nombre_var, 32)

frame_sexo = tk.Frame(card_izq, bg="white")
frame_sexo.pack(fill=tk.X, pady=(0, 15))
tk.Label(frame_sexo, text="Sexo:", bg="white", fg="#374151", font=("Segoe UI", 10)).pack(anchor="w")
radios_sexo.pack(anchor="w", pady=(4, 0))
ttk.Radiobutton(radios_sexo, text="Hombre", value="hombre", variable=sexo_var).pack(side=tk.LEFT, padx=(0, 12))
ttk.Radiobutton(radios_sexo, text="Mujer", value="mujer", variable=sexo_var).pack(side=tk.LEFT)

_crear_campo(card_izq, "[#] Edad (anos):", edad_var, 14)
_crear_campo(card_izq, "[M] Altura (m) - use punto, ej. 1.55:", altura_var, 18)

_crear_combo(card_izq, "Temporada:", temporada_var, ["Verano", "Invierno"], 16)
_crear_combo(card_izq, "Nivel de actividad fisica:", actividad_var, list(FACTOR_ACTIVIDAD.keys()), 20)

frame_combo_pac = tk.Frame(card_der, bg="white")
frame_combo_pac.pack(fill=tk.X, pady=(0, 15))
tk.Label(frame_combo_pac, text="Paciente guardado:", bg="white", fg="#374151", font=("Segoe UI", 10)).pack(anchor="w")
paciente_combo = ttk.Combobox(frame_combo_pac, width=42, state="readonly")

frame_buscar = tk.Frame(card_der, bg="white")
frame_buscar.pack(fill=tk.X, pady=(0, 15))
tk.Label(frame_buscar, text="Buscar enfermedad:", bg="white", fg="#374151", font=("Segoe UI", 10)).pack(anchor="w")
ttk.Entry(frame_buscar, textvariable=buscar_enf_var, width=30).pack(fill=tk.X, pady=(4, 0))
frame_lista_enf = tk.Frame(card_der, bg="white")
frame_lista_enf.pack(fill=tk.BOTH, pady=(0, 8))
scroll_enf = ttk.Scrollbar(frame_lista_enf, orient=tk.VERTICAL)
enfermedades_listbox = tk.Listbox(
    frame_lista_enf,
    selectmode=tk.MULTIPLE,
    exportselection=False,
    yscrollcommand=scroll_enf.set,
)
scroll_enf.config(command=enfermedades_listbox.yview)
enfermedades_listbox.pack(side=tk.LEFT, fill=tk.BOTH, expand=True)

tk.Label(card_der, textvariable=resumen_enf_var, bg="white", fg="#4b5563", justify=tk.LEFT, wraplength=420).pack(anchor="w", pady=(0, 15))

_crear_campo(card_der, "Alergias a medicamentos (texto libre):", alergias_med_var, 44)
_crear_campo(card_der, "ID paciente (opcional; se asigna al guardar):", paciente_id_var, 36)

def _actualizar_resumen_enfermedades():
    if not enfermedades_seleccionadas:
        resumen_enf_var.set("Sin enfermedades seleccionadas")
        return


def _actualizar_lista_enfermedades(*_):
    nonlocal_ref = ENFERMEDADES_OPCIONES
    filtro = normaliza(buscar_enf_var.get().strip())
    enfermedades_filtradas.clear()
    for key, txt in nonlocal_ref:
        if filtro and filtro not in normaliza(txt):
            continue
        enfermedades_filtradas.append((key, txt))
    for idx, (key, _) in enumerate(enfermedades_filtradas):
        if key in enfermedades_seleccionadas:
            enfermedades_listbox.selection_set(idx)
    _actualizar_resumen_enfermedades()
def _on_select_enfermedades(_event=None):
    visibles = {key for key, _ in enfermedades_filtradas}
    enfermedades_seleccionadas.difference_update(visibles)
    for idx in enfermedades_listbox.curselection():
        if 0 <= idx < len(enfermedades_filtradas):
            enfermedades_seleccionadas.add(enfermedades_filtradas[idx][0])
    _actualizar_resumen_enfermedades()


buscar_enf_var.trace_add("write", _actualizar_lista_enfermedades)
enfermedades_listbox.bind("<<ListboxSelect>>", _on_select_enfermedades)


def refrescar_combo_pacientes():
    data = cargar_json()
    paciente_combo["values"] = [f"{p['nombre']} | {p['id']}" for p in data.get("pacientes", [])]
def aplicar_paciente_desde_combo():
    sel = paciente_combo.get()
    if not sel or "|" not in sel:
        messagebox.showinfo("Pacientes", "No hay selección.")
        return
    data = cargar_json()
    for p in data.get("pacientes", []):
        if p.get("id") != pid:
            continue
        nombre_var.set(p.get("nombre", ""))
        edad_var.set(str(p.get("edad", "")))
        sexo_var.set(p.get("sexo", ""))
        temporada_var.set(p.get("temporada", "Verano"))
        actividad_var.set(p.get("actividad", "Moderado"))
        objetivo_var.set(p.get("objetivo", "mantener"))
        enfermedades_seleccionadas.clear()
        for key, _ in ENFERMEDADES_OPCIONES:
            if fl.get(key, False):
                enfermedades_seleccionadas.add(key)
        _actualizar_lista_enfermedades()


btn_frame = ttk.Frame(mainframe)
btn_frame.grid(column=0, row=1, columnspan=2, pady=(8, 12), sticky=(tk.W, tk.E))

resultado_text = tk.Text(mainframe, width=100, height=28, wrap="word", state="disabled")
    nombre = nombre_var.get().strip()
    if not nombre:
        raise ValueError("Indique el nombre del paciente.")
    peso = float(peso_var.get().replace(",", "."))
    altura = float(altura_var.get().replace(",", "."))
        raise ValueError("El peso debe ser mayor que 0.")
    if altura > 2.6:
        raise ValueError("Revise la altura en metros (ej. 1.65, no 165).")
    edad = int(float(edad_var.get().replace(",", ".")))
    if edad < 0 or edad > 120:
    sexo = sexo_var.get().strip().lower()
    if sexo not in ("hombre", "mujer"):
        raise ValueError("Seleccione el sexo del paciente.")
    return nombre, peso, altura, edad, sexo

def construir_flags():
    seleccion = set(enfermedades_seleccionadas)
    return {
        "diabetes": "diabetes" in seleccion,
        "hipertension": "hipertension" in seleccion,
        "obesidad": "obesidad" in seleccion,
        "dislipidemia": "dislipidemia" in seleccion,
        "gastritis": "gastritis" in seleccion,
        "cardiovascular": "cardiovascular" in seleccion,
        "endocrino": "endocrino" in seleccion,
        "renal": "renal" in seleccion,
    }

    m = []
    if flags["diabetes"]:
        m.append("diabetes")
    if flags["hipertension"]:
        m.append("hipertension")
    if flags["obesidad"]:
        m.append(pidemia")
    if flags["gastritis"]:
        m.append("gastritis")
    if flags["anemia"]:
        m.append("anemia")
    if flags["cardiovascular"]:
        m.append("cardiovascular")
    if flags["end"obesidad")
        m.append("disliocrino"]:
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


ttk.Button(btn_frame, text="Cargar paciente seleccionado", command=aplicar_paciente_desde_combo).pack(side=tk.LEFT, padx=4)
tk.Button(
    btn_frame,
    text="Calcular plan",
    command=calcular_desde_formulario,
    bg="#2563eb",
    fg="white",
    activebackground="#1d4ed8",
    activeforeground="white",
    relief=tk.FLAT,
    padx=14,
    pady=6,
).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Guardar datos", command=guardar_paciente_actual).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Guardar en historial", command=guardar_historial).pack(side=tk.LEFT, padx=4)
ttk.Button(btn_frame, text="Exportar PDF", command=exportar_pdf).pack(side=tk.LEFT, padx=4)

refrescar_combo_pacientes()

root.mainloop()
