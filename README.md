<!DOCTYPE html>
<html lang="es" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ANEZKY SPORTS WEAR | Sistema de Inventario & POS</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Orbitron (Sport/Geometric) & Inter -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&family=Orbitron:ital,wght@0,600;0,700;0,800;0,900;1,700;1,800;1,900&display=swap" rel="stylesheet">
    <!-- Lucide Icons -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <!-- jsPDF para exportar a PDF -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf-autotable/3.5.28/jspdf.plugin.autotable.min.js"></script>

    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        anezky: {
                            black: '#07080a',
                            card: '#101216',
                            surface: '#171a21',
                            border: '#232834',
                            cyan: '#00F0FF',
                            cyanDark: '#00b8c4',
                            cyanGlow: 'rgba(0, 240, 255, 0.18)',
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        sport: ['Orbitron', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background-color: #07080a;
            color: #f3f4f6;
            font-family: 'Inter', sans-serif;
            overflow-x: hidden;
            transition: background-color 0.3s ease;
        }
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; height: 8px; }
        ::-webkit-scrollbar-track { background: #0b0d11; }
        ::-webkit-scrollbar-thumb { background: #232834; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #00F0FF; }
        
        .neon-border { box-shadow: 0 0 15px rgba(0, 240, 255, 0.12), inset 0 0 1px rgba(0, 240, 255, 0.4); }
        .neon-text { text-shadow: 0 0 12px rgba(0, 240, 255, 0.45); }
        .neon-btn {
            background: linear-gradient(135deg, #00F0FF 0%, #00b8c4 100%);
            color: #050608;
            box-shadow: 0 4px 20px rgba(0, 240, 255, 0.25);
            transition: all 0.2s ease;
        }
        .neon-btn:hover { box-shadow: 0 6px 25px rgba(0, 240, 255, 0.45); transform: translateY(-1px); }
        .glass-panel {
            background: rgba(16, 18, 22, 0.88);
            backdrop-filter: blur(12px);
            border: 1px solid #232834;
        }

        /* --- ADAPTACIÓN A MODO CLARO --- */
        html.light body { background-color: #f0f2f5; color: #111827; }
        html.light .glass-panel { background: rgba(255, 255, 255, 0.95); border-color: #cbd5e1; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.05); }
        html.light .text-white { color: #0f172a !important; }
        html.light .text-gray-400, html.light .text-gray-300 { color: #475569 !important; }
        html.light .bg-\[\#050608\], html.light .bg-\[\#0c0e12\] { background-color: #e2e8f0 !important; }
        html.light .bg-\[\#090b0e\], html.light .bg-\[\#0b0d11\] { background-color: #ffffff !important; border-color: #cbd5e1 !important; color: #0f172a !important; }
        html.light .border-anezky-border { border-color: #cbd5e1 !important; }
        html.light .bg-anezky-surface { background-color: #f1f5f9 !important; color: #0f172a !important; }
        html.light input, html.light select { color: #0f172a !important; }
        html.light #security-lock-screen { background-color: #e2e8f0; }
    </style>
</head>
<body class="min-h-screen flex flex-col selection:bg-anezky-cyan selection:text-black">

    <!-- ==========================================
         PANTALLA DE BLOQUEO DE SEGURIDAD (PIN)
    =========================================== -->
    <div id="security-lock-screen" class="fixed inset-0 z-50 bg-[#050608] flex flex-col items-center justify-center p-4 transition-opacity duration-300">
        
        <button onclick="toggleTheme()" class="absolute top-6 right-6 p-3 rounded-full bg-anezky-surface border border-anezky-border text-anezky-cyan hover:bg-anezky-cyan hover:text-black transition neon-border z-20" title="Cambiar Modo Claro/Oscuro">
            <i data-lucide="sun" id="theme-icon" class="w-6 h-6"></i>
        </button>

        <div class="absolute w-96 h-96 bg-anezky-cyan/10 rounded-full blur-3xl pointer-events-none"></div>

        <div class="w-full max-w-md glass-panel rounded-2xl p-8 neon-border relative z-10 text-center">
            <!-- Logo ANEZKY -->
            <div class="mx-auto mb-6 relative w-36 h-36 rounded-full bg-black border-2 border-anezky-cyan/50 flex items-center justify-center shadow-[0_0_30px_rgba(0,240,255,0.2)] overflow-hidden">
                <img id="lock-custom-logo" src="anezky_2.jpg" onerror="this.src='https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=100&auto=format&fit=crop&q=60'" class="w-full h-full object-cover" alt="ANEZKY Logo">
            </div>

            <div class="inline-flex items-center gap-1.5 px-3 py-1 rounded-full bg-anezky-cyan/10 border border-anezky-cyan/30 text-anezky-cyan text-xs font-semibold mb-3">
                <i data-lucide="shield-check" class="w-3.5 h-3.5"></i>
                ACCESO PROTEGIDO SHA-256
            </div>

            <h1 class="font-sport text-xl font-bold tracking-wider text-white mb-1">SISTEMA DE INVENTARIO</h1>
            <p class="text-xs text-gray-400 mb-6">Ingresa tu clave de seguridad para gestionar existencias, ventas, entradas y salidas.</p>

            <form id="unlock-form" onsubmit="handleUnlock(event)" class="space-y-4">
                <div class="relative">
                    <i data-lucide="key-round" class="w-5 h-5 text-anezky-cyan absolute left-3.5 top-1/2 -translate-y-1/2"></i>
                    <input 
                        type="password" 
                        id="pin-input" 
                        placeholder="Ingresa clave o PIN" 
                        required
                        autocomplete="current-password"
                        class="w-full pl-11 pr-4 py-3 bg-[#090b0e] border border-anezky-border focus:border-anezky-cyan rounded-xl text-center text-lg tracking-[0.25em] font-bold text-white outline-none transition"
                    >
                </div>
                <p id="lock-error" class="text-xs text-red-400 hidden font-medium">Clave incorrecta. Intenta nuevamente.</p>
                <button type="submit" class="w-full py-3 rounded-xl neon-btn font-sport font-bold text-sm tracking-wider uppercase flex items-center justify-center gap-2">
                    <i data-lucide="unlock" class="w-4 h-4"></i>
                    Desbloquear Sistema
                </button>
            </form>

            <div class="mt-6 pt-4 border-t border-anezky-border/60 flex items-center justify-center text-[11px] text-gray-500">
                <span id="cloud-status-lock" class="flex items-center gap-1 text-emerald-400">
                    <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse"></span> Sistema Encriptado y Sincronizado
                </span>
            </div>
        </div>
    </div>

    <!-- ==========================================
         HEADER / BARRA SUPERIOR
    =========================================== -->
    <header class="sticky top-0 z-30 glass-panel border-b border-anezky-border px-4 lg:px-8 py-3">
        <div class="max-w-7xl mx-auto flex flex-wrap items-center justify-between gap-4">
            <div class="flex items-center gap-3.5">
                <div id="logo-container" onclick="triggerLogoUpload()" title="Haz clic para establecer tu logo permanentemente" class="relative w-14 h-14 rounded-full bg-black border-2 border-anezky-cyan flex items-center justify-center shadow-[0_0_15px_rgba(0,240,255,0.3)] overflow-hidden shrink-0 group cursor-pointer">
                    <img id="header-custom-logo" src="anezky_2.jpg" onerror="this.src='https://images.unsplash.com/photo-1618005182384-a83a8bd57fbe?w=100&auto=format&fit=crop&q=60'" class="w-full h-full object-cover" alt="ANEZKY Logo">
                    <div id="logo-overlay" class="absolute inset-0 bg-black/70 opacity-0 group-hover:opacity-100 flex items-center justify-center transition-opacity">
                        <i data-lucide="camera" class="w-5 h-5 text-anezky-cyan"></i>
                    </div>
                </div>
                <input type="file" id="logo-upload" accept="image/*" class="hidden" onchange="handleLogoUpload(event)">

                <div>
                    <div class="flex items-center gap-2">
                        <h1 class="font-sport text-lg sm:text-xl font-black tracking-wider text-white italic">
                            ANE<span class="text-anezky-cyan neon-text">Z</span>KY <span class="text-xs sm:text-sm font-semibold not-italic text-anezky-cyan tracking-widest">SPORTS WEAR</span>
                        </h1>
                    </div>
                    <div class="flex items-center gap-3 text-xs text-gray-400 mt-0.5">
                        <span id="sync-badge-header" class="inline-flex items-center gap-1.5 text-emerald-400 font-medium">
                            <span class="w-2 h-2 rounded-full bg-emerald-400"></span>
                            Guardado Activo Permanentemente
                        </span>
                    </div>
                </div>
            </div>

            <div class="flex items-center gap-2 sm:gap-3 flex-wrap">
                <!-- BOTÓN GUARDAR TODO -->
                <button onclick="forceSave()" class="px-4 py-2 rounded-lg bg-emerald-500 hover:bg-emerald-400 text-black font-bold text-xs flex items-center gap-1.5 transition shadow-[0_0_15px_rgba(16,185,129,0.3)]">
                    <i data-lucide="save" class="w-4 h-4"></i> Guardar
                </button>

                <select id="currency-selector" onchange="changeCurrency(this.value)" class="bg-anezky-surface border border-anezky-border text-xs font-semibold text-gray-200 rounded-lg px-2.5 py-2 outline-none focus:border-anezky-cyan">
                    <option value="RD$">RD$ (DOP)</option>
                    <option value="$">$ (USD)</option>
                    <option value="MX$">MX$ (MXN)</option>
                    <option value="€">€ (EUR)</option>
                </select>

                <button onclick="openMovementModal('VENTA')" class="px-3.5 py-2 rounded-lg neon-btn font-semibold text-xs flex items-center gap-1.5">
                    <i data-lucide="shopping-cart" class="w-4 h-4"></i>
                    <span>+ Venta</span>
                </button>

                <button onclick="openMovementModal('ENTRADA')" class="px-3 py-2 rounded-lg bg-emerald-500/15 hover:bg-emerald-500/25 text-emerald-400 border border-emerald-500/30 font-semibold text-xs flex items-center gap-1.5 transition">
                    <i data-lucide="arrow-down-left" class="w-4 h-4"></i>
                    <span>+ Entrada</span>
                </button>

                <button onclick="openMovementModal('SALIDA')" class="px-3 py-2 rounded-lg bg-amber-500/15 hover:bg-amber-500/25 text-amber-400 border border-amber-500/30 font-semibold text-xs flex items-center gap-1.5 transition">
                    <i data-lucide="arrow-up-right" class="w-4 h-4"></i>
                    <span>+ Salida</span>
                </button>

                <button onclick="lockSystem()" title="Bloquear Sistema" class="p-2 rounded-lg bg-anezky-surface hover:bg-red-500/20 text-gray-400 hover:text-red-400 border border-anezky-border transition">
                    <i data-lucide="lock" class="w-4 h-4"></i>
                </button>
            </div>
        </div>
    </header>

    <!-- ==========================================
         NAVEGACIÓN DE MÓDULOS (TABS)
    =========================================== -->
    <div class="bg-[#0c0e12] border-b border-anezky-border px-4 lg:px-8">
        <div class="max-w-7xl mx-auto flex items-center gap-1 overflow-x-auto no-scrollbar py-2">
            <button onclick="switchTab('inventario')" id="tab-btn-inventario" class="tab-btn active-tab px-4 py-2.5 rounded-xl font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap transition bg-anezky-cyan/15 text-anezky-cyan border border-anezky-cyan/40">
                <i data-lucide="package" class="w-4 h-4"></i>
                Inventario & Stock
            </button>
            <button onclick="switchTab('movimientos')" id="tab-btn-movimientos" class="tab-btn px-4 py-2.5 rounded-xl font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap transition text-gray-400 hover:text-gray-200">
                <i data-lucide="arrow-left-right" class="w-4 h-4"></i>
                Ventas, Entradas y Salidas (Kardex)
            </button>
            <button onclick="switchTab('seguridad')" id="tab-btn-seguridad" class="tab-btn px-4 py-2.5 rounded-xl font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap transition text-gray-400 hover:text-gray-200">
                <i data-lucide="shield-check" class="w-4 h-4"></i>
                Seguridad & Nube
            </button>
        </div>
    </div>

    <!-- ==========================================
         CONTENIDO PRINCIPAL
    =========================================== -->
    <main class="flex-1 max-w-7xl w-full mx-auto px-4 lg:px-8 py-6 space-y-6">

        <!-- KPI CARDS -->
        <section class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
            <div class="glass-panel rounded-2xl p-5 border-l-4 border-l-anezky-cyan relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-xs uppercase tracking-wider text-gray-400 font-semibold">Unidades en Inventario</p>
                        <h3 id="kpi-total-units" class="font-sport text-2xl sm:text-3xl font-bold text-white mt-1">0</h3>
                        <p id="kpi-total-skus" class="text-xs text-anezky-cyan mt-1">0 referencias registradas</p>
                    </div>
                    <div class="w-12 h-12 rounded-xl bg-anezky-cyan/10 border border-anezky-cyan/30 flex items-center justify-center text-anezky-cyan">
                        <i data-lucide="layers" class="w-6 h-6"></i>
                    </div>
                </div>
            </div>
            <div class="glass-panel rounded-2xl p-5 border-l-4 border-l-blue-500 relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-xs uppercase tracking-wider text-gray-400 font-semibold">Valor en Stock (Venta)</p>
                        <h3 id="kpi-stock-value" class="font-sport text-2xl sm:text-3xl font-bold text-white mt-1">RD$ 0</h3>
                        <p id="kpi-stock-cost" class="text-xs text-gray-400 mt-1">Costo base: RD$ 0</p>
                    </div>
                    <div class="w-12 h-12 rounded-xl bg-blue-500/10 border border-blue-500/30 flex items-center justify-center text-blue-400">
                        <i data-lucide="wallet" class="w-6 h-6"></i>
                    </div>
                </div>
            </div>
            <div class="glass-panel rounded-2xl p-5 border-l-4 border-l-emerald-500 relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-xs uppercase tracking-wider text-gray-400 font-semibold">Ventas Registradas</p>
                        <h3 id="kpi-total-sales" class="font-sport text-2xl sm:text-3xl font-bold text-emerald-400 mt-1">RD$ 0</h3>
                        <p id="kpi-net-profit" class="text-xs text-emerald-500 mt-1">Ganancia est.: RD$ 0</p>
                    </div>
                    <div class="w-12 h-12 rounded-xl bg-emerald-500/10 border border-emerald-500/30 flex items-center justify-center text-emerald-400">
                        <i data-lucide="trending-up" class="w-6 h-6"></i>
                    </div>
                </div>
            </div>
            <div class="glass-panel rounded-2xl p-5 border-l-4 border-l-amber-500 relative overflow-hidden">
                <div class="flex items-center justify-between">
                    <div>
                        <p class="text-xs uppercase tracking-wider text-gray-400 font-semibold">Alertas de Stock Bajo</p>
                        <h3 id="kpi-low-stock" class="font-sport text-2xl sm:text-3xl font-bold text-amber-400 mt-1">0</h3>
                        <p id="kpi-inout-summary" class="text-xs text-gray-400 mt-1">Entradas: 0 | Salidas: 0</p>
                    </div>
                    <div class="w-12 h-12 rounded-xl bg-amber-500/10 border border-amber-500/30 flex items-center justify-center text-amber-400">
                        <i data-lucide="alert-triangle" class="w-6 h-6"></i>
                    </div>
                </div>
            </div>
        </section>

        <!-- ==========================================
             TAB 1: CATÁLOGO DE INVENTARIO
        =========================================== -->
        <section id="section-inventario" class="space-y-4">
            <div class="glass-panel rounded-2xl p-4 flex flex-col lg:flex-row items-stretch lg:items-center justify-between gap-4">
                <div class="flex flex-1 flex-col sm:flex-row items-stretch sm:items-center gap-3">
                    <div class="relative flex-1">
                        <i data-lucide="search" class="w-4 h-4 text-gray-400 absolute left-3.5 top-1/2 -translate-y-1/2"></i>
                        <input type="text" id="search-inventory" oninput="renderInventory()" placeholder="Buscar por artículo, SKU, talla o color..." class="w-full pl-10 pr-4 py-2.5 bg-[#090b0e] border border-anezky-border focus:border-anezky-cyan rounded-xl text-sm text-white outline-none">
                    </div>
                    <select id="filter-category" onchange="renderInventory()" class="bg-[#090b0e] border border-anezky-border focus:border-anezky-cyan rounded-xl px-3.5 py-2.5 text-sm text-gray-200 outline-none">
                        <option value="ALL">Todas las Categorías</option>
                        <option value="Calzado / Cleats">Calzado / Cleats</option>
                        <option value="Guantes">Guantes de Recepción</option>
                        <option value="Equipamiento Flag">Equipamiento Flag</option>
                        <option value="Protección">Protección & Cascos</option>
                        <option value="Ropa Deportiva">Ropa Deportiva / Jerseys</option>
                        <option value="Venta Rápida">Venta Rápida (Sueltos)</option>
                        <option value="Accesorios">Accesorios</option>
                    </select>
                    <select id="filter-status" onchange="renderInventory()" class="bg-[#090b0e] border border-anezky-border focus:border-anezky-cyan rounded-xl px-3.5 py-2.5 text-sm text-gray-200 outline-none">
                        <option value="ALL">Todos los Estados</option>
                        <option value="OK">En Stock Óptimo</option>
                        <option value="LOW">Stock Bajo (Alerta)</option>
                        <option value="OUT">Agotado (0 o menos)</option>
                    </select>
                </div>
                <div class="flex items-center gap-2.5">
                    <button onclick="openProductModal()" class="px-4 py-2.5 rounded-xl neon-btn font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap">
                        <i data-lucide="plus-circle" class="w-4 h-4"></i> Nuevo Artículo
                    </button>
                </div>
            </div>

            <div class="glass-panel rounded-2xl overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-anezky-border bg-[#0b0d11] text-[11px] font-bold uppercase tracking-wider text-gray-400">
                                <th class="py-3.5 px-4">SKU / Artículo</th>
                                <th class="py-3.5 px-4">Categoría</th>
                                <th class="py-3.5 px-4">Talla / Variante</th>
                                <th class="py-3.5 px-4 text-center">Stock Actual</th>
                                <th class="py-3.5 px-4 text-right">Costo Unit.</th>
                                <th class="py-3.5 px-4 text-right">Precio Venta</th>
                                <th class="py-3.5 px-4 text-center">Operación Rápida</th>
                                <th class="py-3.5 px-4 text-right">Acciones</th>
                            </tr>
                        </thead>
                        <tbody id="inventory-table-body" class="divide-y divide-anezky-border/60 text-sm">
                            <!-- Renderizado dinámico por JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ==========================================
             TAB 2: HISTORIAL DE VENTAS, ENTRADAS Y SALIDAS + EXPORTACIÓN
        =========================================== -->
        <section id="section-movimientos" class="space-y-4 hidden">
            <div class="glass-panel rounded-2xl p-4 flex flex-col sm:flex-row items-stretch sm:items-center justify-between gap-4">
                <div class="flex flex-wrap items-center gap-2">
                    <button onclick="filterMovements('ALL')" id="mov-filter-ALL" class="px-3.5 py-2 rounded-xl text-xs font-bold bg-anezky-cyan text-black transition">Todos</button>
                    <button onclick="filterMovements('VENTA')" id="mov-filter-VENTA" class="px-3.5 py-2 rounded-xl text-xs font-semibold bg-anezky-surface text-gray-400 hover:text-white border border-anezky-border transition">🛒 Ventas</button>
                    <button onclick="filterMovements('ENTRADA')" id="mov-filter-ENTRADA" class="px-3.5 py-2 rounded-xl text-xs font-semibold bg-anezky-surface text-gray-400 hover:text-white border border-anezky-border transition">📥 Entradas</button>
                    <button onclick="filterMovements('SALIDA')" id="mov-filter-SALIDA" class="px-3.5 py-2 rounded-xl text-xs font-semibold bg-anezky-surface text-gray-400 hover:text-white border border-anezky-border transition">📤 Salidas</button>
                </div>

                <div class="flex flex-wrap items-center gap-2">
                    <select id="pdf-period-selector" class="bg-[#090b0e] border border-anezky-border focus:border-anezky-cyan rounded-xl px-3 py-2 text-xs font-semibold text-gray-200 outline-none">
                        <option value="DIARIO">Reporte Diario</option>
                        <option value="MENSUAL">Reporte Mensual</option>
                        <option value="ANUAL">Reporte Anual</option>
                        <option value="TODO">Todo el Historial</option>
                    </select>

                    <button onclick="exportMovementPDF()" class="px-3 py-2 rounded-xl bg-red-500/15 hover:bg-red-500/25 text-red-400 text-xs font-semibold flex items-center gap-1.5 border border-red-500/30 transition">
                        <i data-lucide="file-text" class="w-4 h-4"></i> Descargar PDF
                    </button>

                    <button onclick="uploadToDrive()" class="px-3 py-2 rounded-xl bg-blue-500/15 hover:bg-blue-500/25 text-blue-400 text-xs font-semibold flex items-center gap-1.5 border border-blue-500/30 transition">
                        <i data-lucide="cloud-upload" class="w-4 h-4"></i> Subir a Drive
                    </button>
                </div>
            </div>

            <div class="glass-panel rounded-2xl overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="border-b border-anezky-border bg-[#0b0d11] text-[11px] font-bold uppercase tracking-wider text-gray-400">
                                <th class="py-3.5 px-4">Fecha / Hora</th>
                                <th class="py-3.5 px-4">Tipo</th>
                                <th class="py-3.5 px-4">Artículo y Variante</th>
                                <th class="py-3.5 px-4 text-center">Cant.</th>
                                <th class="py-3.5 px-4 text-right">Precio / Desc.</th>
                                <th class="py-3.5 px-4 text-right">Total Operación</th>
                                <th class="py-3.5 px-4">Detalle / Cliente</th>
                                <th class="py-3.5 px-4 text-right">Acciones</th>
                            </tr>
                        </thead>
                        <tbody id="movements-table-body" class="divide-y divide-anezky-border/60 text-sm">
                            <!-- Renderizado dinámico por JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- ==========================================
             TAB 3: SEGURIDAD Y NUBE
        =========================================== -->
        <section id="section-seguridad" class="space-y-6 hidden">
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <!-- CLAVE -->
                <div class="glass-panel rounded-2xl p-6 space-y-4">
                    <div class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-xl bg-anezky-cyan/10 border border-anezky-cyan/30 flex items-center justify-center text-anezky-cyan">
                            <i data-lucide="shield-lock" class="w-5 h-5"></i>
                        </div>
                        <div>
                            <h3 class="font-sport text-sm font-bold text-white">CLAVE DE ACCESO</h3>
                            <p class="text-xs text-gray-400">Protección con persistencia absoluta</p>
                        </div>
                    </div>

                    <form onsubmit="handleChangePin(event)" class="space-y-3 pt-2">
                        <div>
                            <label class="block text-xs text-gray-400 mb-1">Clave Actual</label>
                            <input type="password" id="current-pin-input" required placeholder="••••" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                        </div>
                        <div>
                            <label class="block text-xs text-gray-400 mb-1">Nueva Clave o Contraseña Segura</label>
                            <input type="password" id="new-pin-input" required minlength="4" placeholder="Mínimo 4 caracteres" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                        </div>
                        <button type="submit" class="w-full py-2.5 rounded-xl neon-btn font-bold text-xs uppercase tracking-wider">
                            Actualizar Clave Permanentemente
                        </button>
                    </form>
                </div>

                <!-- FIREBASE NUBE -->
                <div class="glass-panel rounded-2xl p-6 space-y-4 border border-anezky-cyan/30 shadow-[0_0_20px_rgba(0,240,255,0.1)]">
                    <div class="flex items-center gap-3">
                        <div class="w-10 h-10 rounded-xl bg-anezky-cyan/10 border border-anezky-cyan/30 flex items-center justify-center text-anezky-cyan">
                            <i data-lucide="cloud" class="w-5 h-5"></i>
                        </div>
                        <div>
                            <h3 class="font-sport text-sm font-bold text-anezky-cyan">NUBE MULTI-DISPOSITIVO</h3>
                            <p class="text-xs text-gray-400">Para compartir los mismos datos y claves en otros celulares.</p>
                        </div>
                    </div>

                    <p class="text-xs text-gray-300">
                        Las páginas web guardan la información <b>sólo en el dispositivo actual</b>. Para que otros celulares vean los cambios, ingresa las claves de tu base de datos Firebase aquí:
                    </p>

                    <textarea id="firebase-config-input" rows="4" placeholder='{"apiKey": "...", "authDomain": "...", "projectId": "..."}' class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl font-mono text-xs text-gray-400 outline-none focus:border-anezky-cyan"></textarea>
                    
                    <button onclick="saveCustomFirebaseConfig()" class="w-full py-2.5 rounded-xl border border-anezky-cyan/50 text-anezky-cyan font-bold text-xs uppercase tracking-wider hover:bg-anezky-cyan hover:text-black transition">
                        Conectar a la Nube
                    </button>
                </div>
            </div>
        </section>

    </main>

    <!-- ==========================================
         MODAL: CREAR / EDITAR ARTÍCULO
    =========================================== -->
    <div id="product-modal" class="fixed inset-0 z-40 bg-black/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="w-full max-w-lg glass-panel rounded-2xl neon-border overflow-hidden">
            <div class="px-6 py-4 bg-[#0b0d11] border-b border-anezky-border flex items-center justify-between">
                <h3 id="product-modal-title" class="font-sport text-base font-bold text-white tracking-wider">NUEVO ARTÍCULO</h3>
                <button onclick="closeProductModal()" class="text-gray-400 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="product-form" onsubmit="saveProduct(event)" class="p-6 space-y-4">
                <input type="hidden" id="prod-id">
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div class="sm:col-span-1">
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Código / SKU *</label>
                        <input type="text" id="prod-sku" required class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white uppercase outline-none focus:border-anezky-cyan">
                    </div>
                    <div class="sm:col-span-2">
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Nombre del Artículo *</label>
                        <input type="text" id="prod-name" required class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                    </div>
                </div>
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Categoría *</label>
                        <select id="prod-category" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                            <option value="Calzado / Cleats">Calzado / Cleats</option>
                            <option value="Guantes">Guantes de Recepción</option>
                            <option value="Equipamiento Flag">Equipamiento Flag</option>
                            <option value="Protección">Protección & Cascos</option>
                            <option value="Ropa Deportiva">Ropa Deportiva / Jerseys</option>
                            <option value="Accesorios">Accesorios</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Talla / Color / Variante</label>
                        <input type="text" id="prod-variant" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                    </div>
                </div>
                <div class="grid grid-cols-2 sm:grid-cols-4 gap-3 pt-1">
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Stock Inicial</label>
                        <input type="number" id="prod-stock" required value="0" class="w-full px-3 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white font-bold outline-none focus:border-anezky-cyan">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Stock Mínimo</label>
                        <input type="number" id="prod-min" required min="0" value="5" class="w-full px-3 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Costo Unit.</label>
                        <input type="number" id="prod-cost" required min="0" step="0.01" class="w-full px-3 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                    </div>
                    <div>
                        <label class="block text-xs font-semibold text-anezky-cyan mb-1">Precio Venta</label>
                        <input type="number" id="prod-price" required min="0" step="0.01" class="w-full px-3 py-2 bg-[#090b0e] border border-anezky-cyan/60 rounded-xl text-sm text-white font-bold outline-none focus:border-anezky-cyan">
                    </div>
                </div>
                <div class="flex items-center justify-end gap-3 pt-4 border-t border-anezky-border">
                    <button type="button" onclick="closeProductModal()" class="px-4 py-2.5 rounded-xl bg-anezky-surface text-gray-400 hover:text-white text-xs font-semibold">Cancelar</button>
                    <button type="submit" class="px-5 py-2.5 rounded-xl neon-btn font-sport font-bold text-xs tracking-wider uppercase">Guardar Artículo</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==========================================
         MODAL: REGISTRAR MOVIMIENTO (CON DESCUENTOS Y VENTA LIBRE)
    =========================================== -->
    <div id="movement-modal" class="fixed inset-0 z-40 bg-black/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="w-full max-w-lg glass-panel rounded-2xl neon-border overflow-hidden">
            <div id="movement-modal-header" class="px-6 py-4 bg-[#0b0d11] border-b border-anezky-border flex items-center justify-between">
                <div class="flex items-center gap-2.5">
                    <span id="movement-modal-badge" class="px-2.5 py-1 rounded-md text-xs font-bold uppercase bg-anezky-cyan text-black">VENTA</span>
                    <h3 id="movement-modal-title" class="font-sport text-base font-bold text-white tracking-wider">REGISTRAR OPERACIÓN</h3>
                </div>
                <button onclick="closeMovementModal()" class="text-gray-400 hover:text-white"><i data-lucide="x" class="w-5 h-5"></i></button>
            </div>
            <form id="movement-form" onsubmit="saveMovement(event)" class="p-6 space-y-4">
                <input type="hidden" id="mov-type" value="VENTA">
                
                <div class="grid grid-cols-3 gap-2 p-1 bg-[#090b0e] rounded-xl border border-anezky-border">
                    <button type="button" onclick="setMovementType('VENTA')" id="btn-type-VENTA" class="py-2 rounded-lg text-xs font-bold transition">🛒 Venta</button>
                    <button type="button" onclick="setMovementType('ENTRADA')" id="btn-type-ENTRADA" class="py-2 rounded-lg text-xs font-bold transition">📥 Entrada</button>
                    <button type="button" onclick="setMovementType('SALIDA')" id="btn-type-SALIDA" class="py-2 rounded-lg text-xs font-bold transition">📤 Salida</button>
                </div>
                
                <div>
                    <label class="block text-xs font-semibold text-gray-400 mb-1">Seleccionar Artículo *</label>
                    <select id="mov-product-id" required onchange="onMovementProductChange()" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan"></select>
                    
                    <div id="mov-manual-fields" class="hidden mt-2 space-y-2">
                        <input type="text" id="mov-manual-name" placeholder="Nombre del artículo a registrar..." class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-cyan/50 rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                        <div class="grid grid-cols-2 gap-2">
                            <input type="text" id="mov-manual-size" placeholder="Talla / Size (Ej: L)" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-cyan/50 rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                            <input type="text" id="mov-manual-color" placeholder="Color (Ej: Negro)" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-cyan/50 rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                        </div>
                    </div>
                    
                    <p id="mov-stock-hint" class="text-xs text-anezky-cyan mt-1 font-medium"></p>
                </div>
                
                <div class="grid grid-cols-1 sm:grid-cols-3 gap-4">
                    <div>
                        <label class="block text-xs font-semibold text-gray-400 mb-1">Cantidad *</label>
                        <input type="number" id="mov-qty" required min="1" value="1" oninput="updateMovementTotalPreview()" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-border rounded-xl text-sm font-bold text-white outline-none focus:border-anezky-cyan">
                    </div>
                    <div>
                        <label id="mov-price-label" class="block text-xs font-semibold text-gray-400 mb-1">Precio Unitario *</label>
                        <input type="number" id="mov-unit-price" required min="0" step="0.01" oninput="updateMovementTotalPreview()" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-anezky-border rounded-xl text-sm font-bold text-white outline-none focus:border-anezky-cyan">
                    </div>
                    <!-- CAMPO DE DESCUENTO -->
                    <div id="div-descuento">
                        <label class="block text-xs font-semibold text-amber-400 mb-1">Descuento Total ($)</label>
                        <input type="number" id="mov-discount" min="0" step="0.01" value="0" oninput="updateMovementTotalPreview()" class="w-full px-3.5 py-2.5 bg-[#090b0e] border border-amber-500/50 rounded-xl text-sm font-bold text-white outline-none focus:border-amber-400" placeholder="Ej: 500">
                    </div>
                </div>
                
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                    <div>
                        <label id="mov-party-label" class="block text-xs font-semibold text-gray-400 mb-1">Cliente / Referencia</label>
                        <input type="text" id="mov-note" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan">
                    </div>
                    <div>
                        <label id="mov-method-label" class="block text-xs font-semibold text-gray-400 mb-1">Método / Motivo</label>
                        <select id="mov-method" class="w-full px-3.5 py-2 bg-[#090b0e] border border-anezky-border rounded-xl text-sm text-white outline-none focus:border-anezky-cyan"></select>
                    </div>
                </div>
                
                <div class="p-3.5 rounded-xl bg-[#090b0e] border border-anezky-border flex flex-col gap-1">
                    <div class="flex justify-between items-center">
                        <span class="text-xs text-gray-500 font-medium">Subtotal:</span>
                        <span id="mov-subtotal-preview" class="text-sm font-semibold text-gray-300">RD$ 0.00</span>
                    </div>
                    <div class="flex justify-between items-center">
                        <span class="text-xs text-amber-500 font-medium">Descuento aplicado:</span>
                        <span id="mov-discount-preview" class="text-sm font-semibold text-amber-500">- RD$ 0.00</span>
                    </div>
                    <div class="flex justify-between items-center pt-1 border-t border-anezky-border">
                        <span class="text-xs text-gray-300 font-medium">TOTAL FINAL A COBRAR:</span>
                        <span id="mov-total-preview" class="font-sport text-xl font-bold text-anezky-cyan">RD$ 0.00</span>
                    </div>
                </div>
                
                <div class="flex items-center justify-end gap-3 pt-2">
                    <button type="button" onclick="closeMovementModal()" class="px-4 py-2.5 rounded-xl bg-anezky-surface text-gray-400 hover:text-white text-xs font-semibold">Cancelar</button>
                    <button type="submit" class="px-5 py-2.5 rounded-xl neon-btn font-sport font-bold text-xs tracking-wider uppercase">Confirmar Registro</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL POST-VENTA (FACTURA RAPIDA) -->
    <div id="invoice-prompt-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="w-full max-w-sm glass-panel rounded-2xl p-6 neon-border text-center space-y-4">
            <div class="w-16 h-16 rounded-full bg-emerald-500/15 border border-emerald-500/40 text-emerald-400 mx-auto flex items-center justify-center">
                <i data-lucide="check-circle" class="w-8 h-8"></i>
            </div>
            <h4 class="font-sport text-lg font-bold text-white">¡Venta Registrada y Guardada!</h4>
            <p class="text-xs text-gray-400">¿Deseas imprimir o descargar la factura PDF de esta venta?</p>
            
            <div class="space-y-2 pt-2">
                <button id="btn-generate-invoice" class="w-full py-3 rounded-xl neon-btn font-bold text-xs uppercase tracking-wider flex items-center justify-center gap-2 transition">
                    <i data-lucide="printer" class="w-4 h-4"></i> Imprimir Factura PDF
                </button>
                <button onclick="closeInvoicePrompt()" class="w-full py-2.5 rounded-xl bg-anezky-surface text-gray-300 hover:text-white text-xs font-semibold transition border border-transparent hover:border-gray-500">
                    Cerrar y Continuar
                </button>
            </div>
        </div>
    </div>

    <!-- MODAL DE CONFIRMACIÓN -->
    <div id="confirm-modal" class="fixed inset-0 z-50 bg-black/80 backdrop-blur-sm hidden items-center justify-center p-4">
        <div class="w-full max-w-sm glass-panel rounded-2xl p-6 neon-border text-center space-y-4">
            <div class="w-12 h-12 rounded-full bg-red-500/15 border border-red-500/40 text-red-400 mx-auto flex items-center justify-center"><i data-lucide="alert-circle" class="w-6 h-6"></i></div>
            <h4 id="confirm-title" class="font-sport text-base font-bold text-white">¿Confirmar Acción?</h4>
            <p id="confirm-message" class="text-xs text-gray-400"></p>
            <div class="flex items-center justify-center gap-3 pt-2">
                <button onclick="closeConfirmModal()" class="px-4 py-2 rounded-xl bg-anezky-surface text-gray-400 text-xs font-semibold">Cancelar</button>
                <button id="confirm-accept-btn" class="px-4 py-2 rounded-xl bg-red-500 hover:bg-red-600 text-white text-xs font-bold">Sí, Confirmar</button>
            </div>
        </div>
    </div>

    <!-- Notificación Toast -->
    <div id="toast-container" class="fixed bottom-5 right-5 z-50 space-y-2 pointer-events-none"></div>

    <!-- ==========================================
         LÓGICA DEL SISTEMA Y FIREBASE CLOUD
    =========================================== -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, setDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        window.state = { currency: 'RD$', products: [], movements: [], currentMovementFilter: 'ALL', pinHash: '' };

        const STORAGE_DATA_KEY = 'ANEZKY_INVENTORY_DATA';
        const STORAGE_PIN_KEY = 'ANEZKY_SECURE_PIN';
        const STORAGE_LOGO_KEY = 'ANEZKY_CUSTOM_LOGO';
        const STORAGE_THEME_KEY = 'ANEZKY_THEME';

        let db = null, auth = null, currentUser = null, isRemoteUpdating = false;

        window.toggleTheme = function(forceLight = false) {
            const html = document.documentElement; const icon = document.getElementById('theme-icon');
            if (forceLight === true || html.classList.contains('dark')) { html.classList.remove('dark'); html.classList.add('light'); icon.setAttribute('data-lucide', 'moon'); localStorage.setItem(STORAGE_THEME_KEY, 'light'); } 
            else { html.classList.remove('light'); html.classList.add('dark'); icon.setAttribute('data-lucide', 'sun'); localStorage.setItem(STORAGE_THEME_KEY, 'dark'); }
            lucide.createIcons();
        };

        async function sha256(text) {
            const msgBuffer = new TextEncoder().encode(text.trim());
            const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
            return Array.from(new Uint8Array(hashBuffer)).map(b => b.toString(16).padStart(2, '0')).join('');
        }

        async function initAppState() {
            const savedTheme = localStorage.getItem(STORAGE_THEME_KEY); if (savedTheme === 'light') toggleTheme(true);
            const savedLogo = localStorage.getItem(STORAGE_LOGO_KEY); if (savedLogo) applyLogo(savedLogo);

            const defaultHash = await sha256('1234');
            window.state.pinHash = localStorage.getItem(STORAGE_PIN_KEY) || defaultHash;

            try {
                const savedData = localStorage.getItem(STORAGE_DATA_KEY);
                if (savedData) {
                    const p = JSON.parse(savedData);
                    window.state.products = p.products || []; window.state.movements = p.movements || []; window.state.currency = p.currency || 'RD$';
                    if (p.pinHash) window.state.pinHash = p.pinHash;
                }
            } catch (e) {}

            document.getElementById('currency-selector').value = window.state.currency;
            renderAll();
            setupCloudSync();
        }

        window.forceSave = function() {
            window.saveState();
            showToast('¡Datos guardados localmente con éxito!', 'success');
            if(db && currentUser) showToast('Sincronizando con la nube...', 'info');
        };

        window.saveState = function() {
            localStorage.setItem(STORAGE_DATA_KEY, JSON.stringify({ products: window.state.products, movements: window.state.movements, currency: window.state.currency, pinHash: window.state.pinHash }));
            syncToCloud(); renderAll();
        };

        async function setupCloudSync() {
            try {
                const customCfg = localStorage.getItem('ANEZKY_FIREBASE_CONFIG');
                if (!customCfg) return;
                const firebaseConfig = JSON.parse(customCfg);
                
                const app = initializeApp(firebaseConfig);
                auth = getAuth(app); db = getFirestore(app);

                await signInAnonymously(auth);
                onAuthStateChanged(auth, (user) => {
                    currentUser = user; if (!user) return;
                    document.getElementById('sync-badge-header').innerHTML = `<span class="w-2 h-2 rounded-full bg-anezky-cyan animate-ping"></span> Nube Sincronizada`;
                    
                    const docRef = doc(db, 'anezky_store', 'main_state');
                    onSnapshot(docRef, (snap) => {
                        if (snap.exists()) {
                            isRemoteUpdating = true;
                            const d = snap.data();
                            if (d.payload) {
                                const parsed = JSON.parse(d.payload);
                                window.state = { ...window.state, ...parsed };
                                localStorage.setItem(STORAGE_DATA_KEY, JSON.stringify(window.state));
                                if (parsed.pinHash) localStorage.setItem(STORAGE_PIN_KEY, parsed.pinHash);
                                renderAll();
                            }
                            isRemoteUpdating = false;
                        } else { syncToCloud(); }
                    });
                });
            } catch (err) { console.warn('Modo Solo Local Activo'); }
        }

        async function syncToCloud() {
            if (!db || !currentUser || isRemoteUpdating) return;
            try {
                const docRef = doc(db, 'anezky_store', 'main_state');
                await setDoc(docRef, { updatedAt: new Date().toISOString(), payload: JSON.stringify(window.state) });
            } catch (e) {}
        }

        function formatMoney(amount) { return `${window.state.currency} ${Number(amount || 0).toLocaleString('es-DO', { minimumFractionDigits: 2, maximumFractionDigits: 2 })}`; }

        function renderAll() {
            renderKPIs(); renderInventory(); renderMovements(); lucide.createIcons();
        }

        function renderKPIs() {
            let totalUnits = 0, totalValue = 0, totalCost = 0, lowStockCount = 0, totalSales = 0, netProfit = 0, entries = 0, exits = 0;
            window.state.products.forEach(p => { totalUnits += p.stock; totalValue += p.stock * p.price; totalCost += p.stock * p.cost; if (p.stock <= p.minStock) lowStockCount++; });
            window.state.movements.forEach(m => {
                if (m.type === 'VENTA' && !m.revertido) { totalSales += m.total; netProfit += (m.unitPrice - m.unitCost) * m.qty - (m.discount || 0); }
                if (m.type === 'ENTRADA' && !m.revertido) entries += m.qty;
                if (m.type === 'SALIDA' && !m.revertido) exits += m.qty;
            });
            document.getElementById('kpi-total-units').textContent = totalUnits.toLocaleString(); document.getElementById('kpi-total-skus').textContent = `${window.state.products.length} referencias registradas`;
            document.getElementById('kpi-stock-value').textContent = formatMoney(totalValue); document.getElementById('kpi-stock-cost').textContent = `Costo inversión: ${formatMoney(totalCost)}`;
            document.getElementById('kpi-total-sales').textContent = formatMoney(totalSales); document.getElementById('kpi-net-profit').textContent = `Ganancia neta: ${formatMoney(netProfit)}`;
            document.getElementById('kpi-low-stock').textContent = lowStockCount; document.getElementById('kpi-inout-summary').textContent = `Entradas: +${entries} u. | Salidas: -${exits} u.`;
        }

        window.renderInventory = function() {
            const tbody = document.getElementById('inventory-table-body');
            const search = (document.getElementById('search-inventory')?.value || '').toLowerCase().trim();
            const category = document.getElementById('filter-category')?.value || 'ALL';
            const status = document.getElementById('filter-status')?.value || 'ALL';

            const filtered = window.state.products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(search) || p.sku.toLowerCase().includes(search) || (p.variant || '').toLowerCase().includes(search);
                const matchCat = category === 'ALL' || p.category === category;
                let matchStatus = true;
                if (status === 'OK') matchStatus = p.stock > p.minStock;
                if (status === 'LOW') matchStatus = p.stock > 0 && p.stock <= p.minStock;
                if (status === 'OUT') matchStatus = p.stock <= 0;
                return matchSearch && matchCat && matchStatus;
            });

            if (filtered.length === 0) { tbody.innerHTML = `<tr><td colspan="8" class="py-12 text-center text-gray-500">Sin artículos.</td></tr>`; return; }

            tbody.innerHTML = filtered.map(p => {
                let badgeClass = 'bg-emerald-500/15 text-emerald-400 border-emerald-500/30'; let statusText = 'Disponible';
                if (p.stock <= 0) { badgeClass = 'bg-red-500/15 text-red-400 border-red-500/30'; statusText = 'Agotado / Auto'; }
                else if (p.stock <= p.minStock) { badgeClass = 'bg-amber-500/15 text-amber-400 border-amber-500/30'; statusText = 'Stock Bajo'; }

                return `<tr class="hover:bg-black/20 transition"><td class="py-3.5 px-4"><div class="font-bold text-white">${escapeHtml(p.name)}</div><div class="text-xs font-mono text-anezky-cyan">${escapeHtml(p.sku)}</div></td><td class="py-3.5 px-4"><span class="px-2.5 py-1 rounded-lg bg-anezky-surface border border-anezky-border text-xs text-gray-400">${escapeHtml(p.category)}</span></td><td class="py-3.5 px-4 text-gray-400 text-xs">${escapeHtml(p.variant || 'Estándar')}</td><td class="py-3.5 px-4 text-center"><div class="inline-flex flex-col items-center"><span class="px-3 py-1 rounded-full border text-xs font-bold ${badgeClass}">${p.stock} u. • ${statusText}</span><span class="text-[10px] text-gray-500 mt-0.5">Mín: ${p.minStock}</span></div></td><td class="py-3.5 px-4 text-right text-gray-400 font-mono text-xs">${formatMoney(p.cost)}</td><td class="py-3.5 px-4 text-right font-bold text-white font-mono">${formatMoney(p.price)}</td><td class="py-3.5 px-4"><div class="flex items-center justify-center gap-1.5"><button onclick="openMovementModal('VENTA', '${p.id}')" class="px-2.5 py-1.5 rounded-lg bg-anezky-cyan/15 hover:bg-anezky-cyan text-anezky-cyan hover:text-black border border-anezky-cyan/40 text-xs font-bold transition">Vender</button><button onclick="openMovementModal('ENTRADA', '${p.id}')" class="px-2 py-1.5 rounded-lg bg-emerald-500/15 hover:bg-emerald-500 text-emerald-400 hover:text-black border border-emerald-500/30 text-xs font-bold transition">+E</button><button onclick="openMovementModal('SALIDA', '${p.id}')" class="px-2 py-1.5 rounded-lg bg-amber-500/15 hover:bg-amber-500 text-amber-400 hover:text-black border border-amber-500/30 text-xs font-bold transition">-S</button></div></td><td class="py-3.5 px-4 text-right"><div class="inline-flex items-center gap-1"><button onclick="openProductModal('${p.id}')" class="p-1.5 rounded-lg hover:bg-anezky-surface text-gray-400 hover:text-anezky-cyan"><i data-lucide="edit-3" class="w-4 h-4"></i></button><button onclick="confirmDeleteProduct('${p.id}')" class="p-1.5 rounded-lg hover:bg-red-500/15 text-gray-400 hover:text-red-400"><i data-lucide="trash-2" class="w-4 h-4"></i></button></div></td></tr>`;
            }).join(''); lucide.createIcons();
        };

        window.filterMovements = function(type) {
            window.state.currentMovementFilter = type;
            ['ALL', 'VENTA', 'ENTRADA', 'SALIDA'].forEach(t => { const btn = document.getElementById(`mov-filter-${t}`); if (btn) btn.className = (t === type) ? 'px-3.5 py-2 rounded-xl text-xs font-bold bg-anezky-cyan text-black transition' : 'px-3.5 py-2 rounded-xl text-xs font-semibold bg-anezky-surface text-gray-400 hover:text-white border border-anezky-border transition'; });
            renderMovements();
        };

        function renderMovements() {
            const tbody = document.getElementById('movements-table-body');
            const filtered = window.state.movements.filter(m => window.state.currentMovementFilter === 'ALL' || m.type === window.state.currentMovementFilter);
            if (filtered.length === 0) { tbody.innerHTML = `<tr><td colspan="8" class="py-12 text-center text-gray-500">No hay movimientos.</td></tr>`; return; }

            tbody.innerHTML = filtered.map(m => {
                const d = new Date(m.date); const dateStr = d.toLocaleDateString('es-DO', { day: '2-digit', month: 'short', year: 'numeric' }); const timeStr = d.toLocaleTimeString('es-DO', { hour: '2-digit', minute: '2-digit' });
                let typeBadge = '', qtySign = '';
                if (m.type === 'VENTA') { typeBadge = `<span class="px-2.5 py-1 rounded-full bg-anezky-cyan/15 text-anezky-cyan border border-anezky-cyan/30 text-xs font-bold">🛒 VENTA</span>`; qtySign = `<span class="text-anezky-cyan font-bold">-${m.qty}</span>`; }
                else if (m.type === 'ENTRADA') { typeBadge = `<span class="px-2.5 py-1 rounded-full bg-emerald-500/15 text-emerald-400 border border-emerald-500/30 text-xs font-bold">📥 ENTRADA</span>`; qtySign = `<span class="text-emerald-400 font-bold">+${m.qty}</span>`; }
                else { typeBadge = `<span class="px-2.5 py-1 rounded-full bg-amber-500/15 text-amber-400 border border-amber-500/30 text-xs font-bold">📤 SALIDA</span>`; qtySign = `<span class="text-amber-400 font-bold">-${m.qty}</span>`; }

                const variantDisplay = m.variant ? `<br><span class="text-[10px] text-gray-400">${escapeHtml(m.variant)}</span>` : '';
                const discountDisplay = (m.discount > 0 && m.type === 'VENTA') ? `<br><span class="text-[10px] text-amber-500">Desc: -${formatMoney(m.discount)}</span>` : '';

                return `<tr class="hover:bg-black/20 transition ${m.revertido ? 'opacity-40 line-through' : ''}"><td class="py-3.5 px-4 text-xs text-gray-300"><div class="font-semibold text-white">${dateStr}</div><div class="text-gray-500">${timeStr}</div></td><td class="py-3.5 px-4">${typeBadge}</td><td class="py-3.5 px-4"><div class="font-semibold text-white">${escapeHtml(m.productName)}${variantDisplay}</div><div class="text-xs font-mono text-gray-400">${escapeHtml(m.sku || '')}</div></td><td class="py-3.5 px-4 text-center text-sm">${qtySign}</td><td class="py-3.5 px-4 text-right font-mono text-xs text-gray-300">${formatMoney(m.unitPrice)}${discountDisplay}</td><td class="py-3.5 px-4 text-right font-mono font-bold text-white">${formatMoney(m.total)}</td><td class="py-3.5 px-4 text-xs"><div class="text-gray-200">${escapeHtml(m.note || 'Sin nota')}</div><div class="text-gray-500">${escapeHtml(m.method || '')}</div></td><td class="py-3.5 px-4 text-right"><div class="flex flex-col gap-1.5 items-end">${m.type === 'VENTA' && !m.revertido ? `<button onclick="printInvoice('${m.id}')" class="px-2.5 py-1 rounded-lg bg-blue-500/10 hover:bg-blue-500/20 text-blue-400 border border-blue-500/20 text-xs font-semibold transition flex items-center gap-1"><i data-lucide="printer" class="w-3 h-3"></i> Factura</button>` : ''}${!m.revertido ? `<button onclick="confirmRevertMovement('${m.id}')" class="px-2.5 py-1 rounded-lg bg-red-500/10 hover:bg-red-500/20 text-red-400 border border-red-500/20 text-xs font-semibold transition">Revertir</button>` : '<span class="text-xs text-gray-500 italic">Anulado</span>'}</div></td></tr>`;
            }).join(''); lucide.createIcons();
        }

        window.openProductModal = function(productId = null) {
            document.getElementById('product-form').reset(); const title = document.getElementById('product-modal-title');
            if (productId) {
                const p = window.state.products.find(item => item.id === productId);
                title.textContent = 'EDITAR ARTÍCULO'; document.getElementById('prod-id').value = p.id; document.getElementById('prod-sku').value = p.sku; document.getElementById('prod-name').value = p.name; document.getElementById('prod-category').value = p.category; document.getElementById('prod-variant').value = p.variant || ''; document.getElementById('prod-stock').value = p.stock; document.getElementById('prod-min').value = p.minStock; document.getElementById('prod-cost').value = p.cost; document.getElementById('prod-price').value = p.price;
            } else { title.textContent = 'NUEVO ARTÍCULO'; document.getElementById('prod-id').value = ''; document.getElementById('prod-sku').value = `ANZ-SP-${String(window.state.products.length + 1).padStart(2, '0')}`; }
            document.getElementById('product-modal').classList.remove('hidden'); document.getElementById('product-modal').classList.add('flex');
        };

        window.closeProductModal = function() { document.getElementById('product-modal').classList.add('hidden'); document.getElementById('product-modal').classList.remove('flex'); };

        window.saveProduct = function(e) {
            e.preventDefault(); const id = document.getElementById('prod-id').value;
            const pd = { id: id || 'anz-' + Date.now(), sku: document.getElementById('prod-sku').value.trim().toUpperCase(), name: document.getElementById('prod-name').value.trim(), category: document.getElementById('prod-category').value, variant: document.getElementById('prod-variant').value.trim(), stock: Number(document.getElementById('prod-stock').value) || 0, minStock: Number(document.getElementById('prod-min').value) || 0, cost: Number(document.getElementById('prod-cost').value) || 0, price: Number(document.getElementById('prod-price').value) || 0 };
            if (id) { const idx = window.state.products.findIndex(p => p.id === id); if (idx !== -1) window.state.products[idx] = pd; showToast('Artículo actualizado', 'success'); } else { window.state.products.unshift(pd); showToast('Nuevo artículo agregado', 'success'); }
            window.saveState(); closeProductModal();
        };

        window.confirmDeleteProduct = function(productId) {
            const p = window.state.products.find(item => item.id === productId); if (!p) return;
            openConfirmModal('¿Eliminar Artículo?', `Se eliminará "${p.name}".`, () => { window.state.products = window.state.products.filter(item => item.id !== productId); window.saveState(); showToast('Artículo eliminado', 'info'); });
        };

        window.openMovementModal = function(type = 'VENTA', preselectedProductId = null) {
            const select = document.getElementById('mov-product-id');
            let optionsHTML = `<option value="MANUAL">⚡ Venta Rápida / Libre (Se registra automáticamente)</option>`;
            if (window.state.products.length > 0) { optionsHTML += `<optgroup label="Catálogo de Inventario">` + window.state.products.map(p => `<option value="${p.id}">${escapeHtml(p.sku)} - ${escapeHtml(p.name)} (Stock: ${p.stock})</option>`).join('') + `</optgroup>`; }
            select.innerHTML = optionsHTML; if (preselectedProductId) select.value = preselectedProductId;
            
            document.getElementById('mov-qty').value = 1; document.getElementById('mov-note').value = ''; document.getElementById('mov-unit-price').value = ''; document.getElementById('mov-manual-name').value = ''; document.getElementById('mov-manual-size').value = ''; document.getElementById('mov-manual-color').value = ''; document.getElementById('mov-discount').value = 0;
            
            setMovementType(type); document.getElementById('movement-modal').classList.remove('hidden'); document.getElementById('movement-modal').classList.add('flex');
        };

        window.closeMovementModal = function() { document.getElementById('movement-modal').classList.add('hidden'); document.getElementById('movement-modal').classList.remove('flex'); };

        window.setMovementType = function(type) {
            document.getElementById('mov-type').value = type; const badge = document.getElementById('movement-modal-badge'); const pLabel = document.getElementById('mov-price-label'); const cLabel = document.getElementById('mov-party-label'); const mSelect = document.getElementById('mov-method'); const noteInp = document.getElementById('mov-note'); const divDesc = document.getElementById('div-descuento');

            ['VENTA', 'ENTRADA', 'SALIDA'].forEach(t => { document.getElementById(`btn-type-${t}`).className = (t === type) ? 'py-2 rounded-lg text-xs font-bold bg-anezky-cyan text-black transition' : 'py-2 rounded-lg text-xs font-semibold text-gray-400 hover:text-white transition'; });

            if (type === 'VENTA') {
                badge.textContent = 'VENTA'; badge.className = 'px-2.5 py-1 rounded-md text-xs font-bold uppercase bg-anezky-cyan text-black'; pLabel.textContent = 'Precio de Venta Unit. *'; cLabel.textContent = 'Cliente (Obligatorio) *'; noteInp.required = true; noteInp.placeholder = 'Nombre del cliente...'; divDesc.classList.remove('hidden');
                mSelect.innerHTML = `<option value="Efectivo">Efectivo</option><option value="Transferencia Bancaria">Transferencia Bancaria</option><option value="Tarjeta / POS">Tarjeta / POS</option>`;
            } else if (type === 'ENTRADA') {
                badge.textContent = 'ENTRADA'; badge.className = 'px-2.5 py-1 rounded-md text-xs font-bold uppercase bg-emerald-400 text-black'; pLabel.textContent = 'Costo Unitario *'; cLabel.textContent = 'Proveedor / Lote'; noteInp.required = false; noteInp.placeholder = 'Ej: Compra a Fabrica'; divDesc.classList.add('hidden'); document.getElementById('mov-discount').value = 0;
                mSelect.innerHTML = `<option value="Compra a Proveedor">Compra a Proveedor</option><option value="Ajuste (+)">Ajuste (+)</option>`;
            } else {
                badge.textContent = 'SALIDA'; badge.className = 'px-2.5 py-1 rounded-md text-xs font-bold uppercase bg-amber-400 text-black'; pLabel.textContent = 'Costo Ref. Unitario *'; cLabel.textContent = 'Destino / Responsable'; noteInp.required = false; noteInp.placeholder = 'Ej: Merma o Patrocinio'; divDesc.classList.add('hidden'); document.getElementById('mov-discount').value = 0;
                mSelect.innerHTML = `<option value="Patrocinio">Patrocinio</option><option value="Merma">Merma</option><option value="Garantía">Garantía</option>`;
            }
            onMovementProductChange();
        };

        window.onMovementProductChange = function() {
            const prodId = document.getElementById('mov-product-id').value; const type = document.getElementById('mov-type').value; const manualFields = document.getElementById('mov-manual-fields'); const manualNameInput = document.getElementById('mov-manual-name'); const stockHint = document.getElementById('mov-stock-hint'); const priceInput = document.getElementById('mov-unit-price');

            if (prodId === 'MANUAL') {
                manualFields.classList.remove('hidden'); manualNameInput.required = true; stockHint.textContent = 'Se creará automáticamente (Stock en negativo al vender).';
            } else {
                manualFields.classList.add('hidden'); manualNameInput.required = false; const p = window.state.products.find(item => item.id === prodId);
                if (p) { stockHint.textContent = `Stock actual: ${p.stock} unidades`; priceInput.value = (type === 'VENTA') ? p.price : p.cost; }
            }
            updateMovementTotalPreview();
        };

        window.updateMovementTotalPreview = function() {
            const qty = Number(document.getElementById('mov-qty').value) || 0; const price = Number(document.getElementById('mov-unit-price').value) || 0; const discount = Number(document.getElementById('mov-discount').value) || 0;
            const subtotal = qty * price; const total = Math.max(0, subtotal - discount);
            document.getElementById('mov-subtotal-preview').textContent = formatMoney(subtotal); document.getElementById('mov-discount-preview').textContent = `- ${formatMoney(discount)}`; document.getElementById('mov-total-preview').textContent = formatMoney(total);
        };

        window.saveMovement = function(e) {
            e.preventDefault(); const type = document.getElementById('mov-type').value; let prodId = document.getElementById('mov-product-id').value; const qty = Number(document.getElementById('mov-qty').value) || 0; const unitPrice = Number(document.getElementById('mov-unit-price').value) || 0; const discount = Number(document.getElementById('mov-discount').value) || 0; const note = document.getElementById('mov-note').value.trim(); const method = document.getElementById('mov-method').value;
            let productName = '', sku = '', unitCost = 0, variantStr = '';

            if (prodId === 'MANUAL') {
                productName = document.getElementById('mov-manual-name').value.trim(); const size = document.getElementById('mov-manual-size').value.trim(); const color = document.getElementById('mov-manual-color').value.trim();
                let vParts = []; if(size) vParts.push(`Talla: ${size}`); if(color) vParts.push(`Color: ${color}`); variantStr = vParts.length > 0 ? vParts.join(' | ') : 'Venta Rápida';
                
                const newProduct = { id: 'anz-vr-' + Date.now(), sku: 'VR-' + Math.floor(Math.random() * 10000), name: productName, category: 'Venta Rápida', variant: variantStr, stock: 0, minStock: 0, cost: (type === 'ENTRADA') ? unitPrice : 0, price: (type === 'VENTA') ? unitPrice : 0 };
                window.state.products.unshift(newProduct); prodId = newProduct.id; sku = newProduct.sku; unitCost = newProduct.cost;
            } else {
                const product = window.state.products.find(p => p.id === prodId); if (!product) return;
                productName = product.name; sku = product.sku; unitCost = product.cost; variantStr = product.variant;
            }

            const actualProduct = window.state.products.find(p => p.id === prodId);
            if (type === 'ENTRADA') actualProduct.stock += qty; else actualProduct.stock -= qty;

            const subtotal = qty * unitPrice; const total = Math.max(0, subtotal - discount);
            const newMov = { id: 'mov-' + Date.now(), date: new Date().toISOString(), type, productId: prodId, productName, sku, qty, unitPrice, unitCost, variant: variantStr, subtotal, discount, total, note: note || 'Sin nota', method, revertido: false };
            
            window.state.movements.unshift(newMov); window.saveState(); closeMovementModal();
            if (type === 'VENTA') showInvoicePrompt(newMov.id); else showToast('Operación registrada exitosamente', 'success');
        };

        function showInvoicePrompt(movId) {
            const btn = document.getElementById('btn-generate-invoice'); btn.onclick = () => { printInvoice(movId); closeInvoicePrompt(); };
            document.getElementById('invoice-prompt-modal').classList.remove('hidden'); document.getElementById('invoice-prompt-modal').classList.add('flex');
        }

        window.closeInvoicePrompt = function() { document.getElementById('invoice-prompt-modal').classList.add('hidden'); document.getElementById('invoice-prompt-modal').classList.remove('flex'); };

        window.printInvoice = function(movId) {
            const m = window.state.movements.find(x => x.id === movId); if (!m) return;
            const { jsPDF } = window.jspdf; const doc = new jsPDF({ format: 'a5' });

            const logoContainer = document.getElementById('logo-container');
            if (logoContainer && logoContainer.title === "Logo Permanente ANEZKY") {
                const logoData = document.getElementById('header-custom-logo').src;
                try { if (logoData.startsWith('data:image')) doc.addImage(logoData, 'JPEG', 14, 10, 30, 30); } catch(e) {}
            } else {
                let img = new Image(); img.src = 'anezky_2.jpg'; doc.addImage(img, 'JPEG', 14, 10, 30, 30);
            }

            doc.setFontSize(14); doc.setFont("helvetica", "bold"); doc.text("ANEZKY SPORTS WEAR", 50, 20);
            doc.setFontSize(10); doc.setFont("helvetica", "normal"); doc.text("Factura de Venta / Recibo Comercial", 50, 26);
            const d = new Date(m.date); doc.text(`Fecha: ${d.toLocaleDateString('es-DO')}  |  Hora: ${d.toLocaleTimeString('es-DO', { hour: '2-digit', minute: '2-digit' })}`, 50, 32);
            
            doc.text(`Ref Ticket: ${m.id.toUpperCase()}`, 14, 50); doc.text(`Cliente: ${m.note || 'Cliente General'}`, 14, 56); doc.text(`Vendedor: Admin / ANEZKY`, 14, 62); doc.text(`Método de Pago: ${m.method || 'Efectivo'}`, 14, 68);

            const prodName = m.variant ? `${m.productName} \n(${m.variant})` : m.productName;
            doc.autoTable({ startY: 75, head: [['Cant.', 'Descripción (Talla/Color)', 'Precio Unit.', 'Subtotal']], body: [[m.qty, prodName, formatMoney(m.unitPrice), formatMoney(m.subtotal)]], theme: 'grid', headStyles: { fillColor: [0, 240, 255], textColor: [0,0,0] }, styles: { fontSize: 9, cellPadding: 3 } });

            const finalY = doc.lastAutoTable.finalY || 100;
            
            if(m.discount > 0) {
                doc.setFontSize(10); doc.text(`Subtotal: ${formatMoney(m.subtotal)}`, 14, finalY + 8);
                doc.setTextColor(239, 68, 68); doc.text(`Descuento aplicado: -${formatMoney(m.discount)}`, 14, finalY + 14); doc.setTextColor(0, 0, 0);
                doc.setFontSize(12); doc.setFont("helvetica", "bold"); doc.text(`TOTAL PAGADO: ${formatMoney(m.total)}`, 14, finalY + 22);
                doc.setFontSize(10); doc.setFont("helvetica", "normal"); doc.text("¡Gracias por tu compra!", 14, finalY + 34); doc.text("Anezky Sports Wear - Vístete para ganar.", 14, finalY + 40);
            } else {
                doc.setFontSize(12); doc.setFont("helvetica", "bold"); doc.text(`TOTAL PAGADO: ${formatMoney(m.total)}`, 14, finalY + 12);
                doc.setFontSize(10); doc.setFont("helvetica", "normal"); doc.text("¡Gracias por tu compra!", 14, finalY + 25); doc.text("Anezky Sports Wear - Vístete para ganar.", 14, finalY + 31);
            }

            doc.save(`Factura_Anezky_${m.id}.pdf`); showToast('Factura generada y descargada', 'success');
        };

        window.confirmRevertMovement = function(movId) {
            const m = window.state.movements.find(item => item.id === movId); if (!m) return;
            openConfirmModal('¿Revertir Operación?', `Se anulará el registro y se restaurará el stock del catálogo.`, () => {
                const p = window.state.products.find(x => x.id === m.productId);
                if (p) { if (m.type === 'ENTRADA') p.stock = Math.max(0, p.stock - m.qty); else p.stock += m.qty; }
                m.revertido = true; window.saveState(); showToast('Operación revertida', 'info');
            });
        };

        window.handleUnlock = async function(e) {
            e.preventDefault(); const input = document.getElementById('pin-input'); const errEl = document.getElementById('lock-error');
            if (await sha256(input.value) === window.state.pinHash) {
                errEl.classList.add('hidden'); input.value = ''; document.getElementById('security-lock-screen').classList.add('opacity-0', 'pointer-events-none'); setTimeout(() => document.getElementById('security-lock-screen').classList.add('hidden'), 300);
            } else { errEl.classList.remove('hidden'); input.value = ''; input.focus(); }
        };

        window.lockSystem = function() {
            document.getElementById('security-lock-screen').classList.remove('hidden'); setTimeout(() => { document.getElementById('security-lock-screen').classList.remove('opacity-0', 'pointer-events-none'); document.getElementById('pin-input').focus(); }, 10);
        };

        window.handleChangePin = async function(e) {
            e.preventDefault(); const cInput = document.getElementById('current-pin-input'); const nInput = document.getElementById('new-pin-input');
            if (await sha256(cInput.value) !== window.state.pinHash) { showToast('La clave actual es incorrecta', 'error'); return; }
            window.state.pinHash = await sha256(nInput.value); localStorage.setItem(STORAGE_PIN_KEY, window.state.pinHash); window.saveState(); cInput.value = ''; nInput.value = ''; showToast('Clave actualizada permanentemente.', 'success');
        };

        window.triggerLogoUpload = function() { const logoContainer = document.getElementById('logo-container'); if (logoContainer && logoContainer.title === "Logo Permanente ANEZKY") return; document.getElementById('logo-upload').click(); };
        window.handleLogoUpload = function(e) { const file = e.target.files?.[0]; if (!file) return; const reader = new FileReader(); reader.onload = function(evt) { const base64Data = evt.target.result; localStorage.setItem(STORAGE_LOGO_KEY, base64Data); applyLogo(base64Data); showToast('Logo ANEZKY actualizado y fijado permanentemente', 'success'); }; reader.readAsDataURL(file); };
        function applyLogo(source) { document.getElementById('header-custom-logo').src = source; document.getElementById('lock-custom-logo').src = source; const logoContainer = document.getElementById('logo-container'); const logoOverlay = document.getElementById('logo-overlay'); if(logoContainer) { logoContainer.onclick = null; logoContainer.classList.remove('cursor-pointer', 'group'); logoContainer.title = "Logo Permanente ANEZKY"; } if(logoOverlay) logoOverlay.style.display = 'none'; }
        window.changeCurrency = function(curr) { window.state.currency = curr; window.saveState(); };
        window.switchTab = function(tabName) { ['inventario', 'movimientos', 'seguridad'].forEach(t => { const sec = document.getElementById(`section-${t}`); const btn = document.getElementById(`tab-btn-${t}`); if (t === tabName) { sec.classList.remove('hidden'); btn.className = 'tab-btn px-4 py-2.5 rounded-xl font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap transition bg-anezky-cyan/15 text-anezky-cyan border border-anezky-cyan/40'; } else { sec.classList.add('hidden'); btn.className = 'tab-btn px-4 py-2.5 rounded-xl font-semibold text-xs sm:text-sm flex items-center gap-2 whitespace-nowrap transition text-gray-400 hover:text-gray-200'; } }); };

        window.exportMovementPDF = function() {
            const period = document.getElementById('pdf-period-selector').value; const now = new Date(); const { jsPDF } = window.jspdf; const doc = new jsPDF();
            const filtered = window.state.movements.filter(m => { const d = new Date(m.date); if (period === 'DIARIO') return d.toDateString() === now.toDateString(); if (period === 'MENSUAL') return d.getMonth() === now.getMonth() && d.getFullYear() === now.getFullYear(); if (period === 'ANUAL') return d.getFullYear() === now.getFullYear(); return true; });
            doc.setFontSize(16); doc.text(`ANEZKY SPORTS WEAR - Kardex de Movimientos`, 14, 15); doc.setFontSize(10); doc.text(`Filtro: ${period} | Fecha de emisión: ${now.toLocaleDateString()}`, 14, 22);
            doc.autoTable({ startY: 30, head: [['Fecha', 'Tipo', 'Artículo', 'Cant.', 'Precio', 'Total', 'Detalle']], body: filtered.map(m => [new Date(m.date).toLocaleDateString(), m.type, m.productName, m.qty, m.unitPrice.toFixed(2), m.total.toFixed(2), m.note || '-']), theme: 'grid', headStyles: { fillColor: [0, 240, 255], textColor: [0,0,0] } });
            doc.save(`Reporte_${period}_ANEZKY.pdf`); showToast('Reporte PDF Generado', 'success');
        };

        window.uploadToDrive = function() { exportMovementPDF(); showToast('Abriendo Google Drive...', 'info'); setTimeout(() => { window.open('https://drive.google.com/drive/my-drive', '_blank'); }, 2000); };
        window.downloadBackupJSON = function() { const blob = new Blob([localStorage.getItem(STORAGE_DATA_KEY) || '{"products":[],"movements":[]}'], { type: 'application/json' }); const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href = url; a.download = `Respaldo_ANEZKY_${new Date().toISOString().slice(0,10)}.json`; a.click(); URL.revokeObjectURL(url); showToast('Copia de seguridad (.JSON) descargada', 'success'); };
        window.restoreBackupJSON = function(e) { const file = e.target.files?.[0]; if (!file) return; const reader = new FileReader(); reader.onload = function(evt) { try { const parsed = JSON.parse(evt.target.result); window.state.products = parsed.products || []; window.state.movements = parsed.movements || []; window.state.currency = parsed.currency || 'RD$'; if(parsed.pinHash) window.state.pinHash = parsed.pinHash; window.saveState(); showToast('Base de datos restaurada.', 'success'); } catch (err) { showToast('JSON inválido', 'error'); } }; reader.readAsText(file); };
        
        let confirmCallback = null; function openConfirmModal(title, message, onConfirm) { document.getElementById('confirm-title').textContent = title; document.getElementById('confirm-message').textContent = message; confirmCallback = onConfirm; document.getElementById('confirm-modal').classList.remove('hidden'); document.getElementById('confirm-modal').classList.add('flex'); }
        window.closeConfirmModal = function() { document.getElementById('confirm-modal').classList.add('hidden'); document.getElementById('confirm-modal').classList.remove('flex'); };
        document.getElementById('confirm-accept-btn').addEventListener('click', () => { if (confirmCallback) confirmCallback(); closeConfirmModal(); });
        function showToast(message, type = 'success') { const container = document.getElementById('toast-container'); const toast = document.createElement('div'); const colors = { success: 'border-anezky-cyan bg-[#0c1015] text-white', error: 'border-red-500 bg-[#160c0e] text-red-200', info: 'border-gray-500 bg-[#101216] text-gray-200' }; toast.className = `px-4 py-3 rounded-xl border-l-4 ${colors[type]} shadow-xl text-xs font-semibold flex items-center gap-2.5 transition-all duration-300`; toast.innerHTML = `<span class="w-2 h-2 rounded-full bg-anezky-cyan"></span><span>${escapeHtml(message)}</span>`; container.appendChild(toast); setTimeout(() => { toast.classList.add('opacity-0', 'translate-x-4'); setTimeout(() => toast.remove(), 300); }, 3400); }
        function escapeHtml(str) { return String(str || '').replace(/&/g, '&amp;').replace(/</g, '&lt;').replace(/>/g, '&gt;').replace(/"/g, '&quot;'); }

        window.addEventListener('DOMContentLoaded', () => { switchTab('inventario'); initAppState(); });
    </script>
</body>
</html>
