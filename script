// ==UserScript==
// @name         Torn War & Combat Advisor (All-In-One Edition)
// @namespace    https://github.com/tdup-torn/userscripts
// @version      0.1a
// @description  Full in-game War & Combat Advisor dashboard for Torn City. Powered by official TDup BSP stats, Fair Fight respect optimizer, weapon/armor audits, and target scouting.
// @author       Dexter786
// @match        https://www.torn.com/*
// @grant        GM_xmlhttpRequest
// @grant        GM_setValue
// @grant        GM_getValue
// @grant        unsafeWindow
// @connect      api.torn.com
// @connect      www.lol-manager.com
// @connect      lol-manager.com
// @connect      corsproxy.io
// @run-at       document-end
// ==/UserScript==

(function() {
    'use strict';

    console.log('[TornWarAdvisor] Initializing All-in-One UserScript v3.4...');

    // Resolve execution window
    const targetWindow = typeof unsafeWindow !== 'undefined' ? unsafeWindow : window;

    // 1. Inject Floating Launcher Button into Torn City UI
    function initLauncher() {
        if (document.getElementById('twa-launcher-btn')) return;

        const btn = document.createElement('div');
        btn.id = 'twa-launcher-btn';
        btn.innerHTML = '⚔️ War Advisor';
        btn.setAttribute('style', 'position: fixed; bottom: 25px; right: 25px; background: linear-gradient(135deg, #ff9900, #ff6600); color: #000; font-weight: 800; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif; font-size: 13px; padding: 10px 18px; border-radius: 30px; cursor: pointer; box-shadow: 0 4px 15px rgba(255, 153, 0, 0.45); z-index: 9999999; display: flex; align-items: center; gap: 8px; border: 2px solid #fff; transition: all 0.2s ease; user-select: none;');

        btn.onmouseover = () => {
            btn.style.transform = 'scale(1.06)';
            btn.style.boxShadow = '0 6px 20px rgba(255, 153, 0, 0.65)';
        };
        btn.onmouseout = () => {
            btn.style.transform = 'scale(1)';
            btn.style.boxShadow = '0 4px 15px rgba(255, 153, 0, 0.45)';
        };
        btn.onclick = toggleModal;

        if (document.body) {
            document.body.appendChild(btn);
        } else if (document.documentElement) {
            document.documentElement.appendChild(btn);
        }
    }

    // 2. Modal Overlay Container
    let modalOverlay = null;

    function createModal() {
        if (document.getElementById('twa-modal-overlay')) {
            modalOverlay = document.getElementById('twa-modal-overlay');
            return;
        }

        modalOverlay = document.createElement('div');
        modalOverlay.id = 'twa-modal-overlay';
        modalOverlay.setAttribute('style', 'display: none; position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; background: rgba(0, 0, 0, 0.85); backdrop-filter: blur(5px); z-index: 10000000; justify-content: center; align-items: center; box-sizing: border-box;');

        const modalBox = document.createElement('div');
        modalBox.id = 'twa-modal-box';
        modalBox.setAttribute('style', 'width: 95vw; max-width: 1050px; height: 90vh; background: #121214; color: #f0f0f5; border-radius: 12px; border: 1px solid #2e2e38; box-shadow: 0 10px 30px rgba(0,0,0,0.9); display: flex; flex-direction: column; overflow: hidden; position: relative; font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; box-sizing: border-box;');

        // Header bar with Close button
        const topBar = document.createElement('div');
        topBar.setAttribute('style', 'padding: 12px 20px; background: #18181d; border-bottom: 1px solid #2e2e38; display: flex; justify-content: space-between; align-items: center; flex-shrink: 0; box-sizing: border-box;');
        topBar.innerHTML = '<div style="display:flex; align-items:center; gap:10px;"><span style="font-size:18px; font-weight:bold; color:#ff9900;">⚔️ Torn War & Combat Advisor</span><span style="font-size:11px; background:#222; border:1px solid #444; border-radius:12px; padding:2px 8px; color:#aaa;">In-Game v3.5</span></div><button id="twa-close-btn" style="background: transparent; border: none; color: #aaa; font-size: 26px; font-weight: bold; cursor: pointer; line-height: 1; padding: 4px 8px;">&times;</button>';

        const scrollContent = document.createElement('div');
        scrollContent.id = 'twa-scroll-content';
        scrollContent.setAttribute('style', 'flex: 1; overflow-y: auto; padding: 20px; box-sizing: border-box;');

        // Inject the strictly scoped CSS styles
        const styleEl = document.createElement('style');
        styleEl.id = 'twa-custom-styles';
        styleEl.textContent = '#twa-modal-overlay * { box-sizing: border-box; }\n' + TWA_EMBEDDED_CSS;

        modalBox.appendChild(topBar);
        modalBox.appendChild(scrollContent);
        modalOverlay.appendChild(styleEl);
        modalOverlay.appendChild(modalBox);
        document.body.appendChild(modalOverlay);

        // Inject HTML into scrollable body
        scrollContent.innerHTML = TWA_EMBEDDED_HTML;

        // Hook close button & ESC key
        document.getElementById('twa-close-btn').onclick = toggleModal;
        modalOverlay.onclick = (e) => {
            if (e.target === modalOverlay) toggleModal();
        };
        document.addEventListener('keydown', (e) => {
            if (e.key === 'Escape' && modalOverlay && modalOverlay.style.display === 'flex') {
                toggleModal();
            }
        });

        // Attach scripts & logic
        initAdvisorLogic();
    }

    function toggleModal() {
        if (!modalOverlay) createModal();
        if (modalOverlay.style.display === 'none' || modalOverlay.style.display === '') {
            modalOverlay.style.display = 'flex';
        } else {
            modalOverlay.style.display = 'none';
        }
    }

    const TWA_EMBEDDED_CSS = "#twa-modal-overlay { --bg-dark: #121214;\n            --card-bg: #1e1e24;\n            --border-color: #2e2e38;\n            --accent-orange: #ff9900;\n            --accent-green: #4caf50;\n            --accent-red: #f44336;\n            --accent-blue: #2196f3;\n            --accent-purple: #9c27b0;\n            --text-main: #f0f0f5;\n            --text-muted: #9595a5; }\r\n#twa-modal-overlay .container { width: 100%;\n            max-width: 950px; }\r\n#twa-modal-overlay .header { text-align: center;\n            margin-bottom: 25px; }\r\n#twa-modal-overlay .header h1 { color: var(--accent-orange);\n            margin: 0 0 8px 0;\n            font-size: 26px; }\r\n#twa-modal-overlay .header p { color: var(--text-muted);\n            margin: 0;\n            font-size: 14px; }\r\n#twa-modal-overlay .api-card { background: var(--card-bg);\n            border: 1px solid var(--border-color);\n            border-radius: 10px;\n            padding: 20px;\n            margin-bottom: 25px;\n            box-shadow: 0 4px 15px rgba(0,0,0,0.4);\n            display: flex;\n            gap: 10px;\n            flex-wrap: wrap; }\r\n#twa-modal-overlay .api-input { flex-grow: 1;\n            background: #141418;\n            border: 1px solid #3e3e4a;\n            color: #fff;\n            padding: 12px 16px;\n            border-radius: 6px;\n            font-size: 14px;\n            letter-spacing: 1px;\n            outline: none; }\r\n#twa-modal-overlay .api-input:focus { border-color: var(--accent-orange); }\r\n#twa-modal-overlay .btn-analyze { background: var(--accent-orange);\n            color: #000;\n            border: none;\n            padding: 12px 24px;\n            font-weight: bold;\n            font-size: 14px;\n            border-radius: 6px;\n            cursor: pointer;\n            transition: 0.2s; }\r\n#twa-modal-overlay .btn-analyze:hover { opacity: 0.9;\n            transform: translateY(-1px); }\r\n#twa-modal-overlay #results { display: none; }\r\n#twa-modal-overlay .grid { display: grid;\n            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));\n            gap: 15px;\n            margin-bottom: 20px; }\r\n#twa-modal-overlay .card { background: var(--card-bg);\n            border: 1px solid var(--border-color);\n            border-radius: 10px;\n            padding: 18px;\n            box-shadow: 0 4px 12px rgba(0,0,0,0.3); }\r\n#twa-modal-overlay .card-title { font-size: 13px;\n            text-transform: uppercase;\n            letter-spacing: 0.5px;\n            color: var(--text-muted);\n            margin-bottom: 10px;\n            display: flex;\n            justify-content: space-between; }\r\n#twa-modal-overlay .card-value { font-size: 22px;\n            font-weight: bold;\n            margin-bottom: 5px; }\r\n#twa-modal-overlay .badge { display: inline-block;\n            padding: 4px 10px;\n            border-radius: 20px;\n            font-size: 12px;\n            font-weight: bold;\n            text-transform: uppercase; }\r\n#twa-modal-overlay .badge-rogue { background: #9c27b0; color: #fff; }\r\n#twa-modal-overlay .badge-tank { background: #2196f3; color: #fff; }\r\n#twa-modal-overlay .badge-balanced { background: #4caf50; color: #fff; }\r\n#twa-modal-overlay .badge-glass { background: #f44336; color: #fff; }\r\n#twa-modal-overlay .stat-bar-container { margin: 10px 0; }\r\n#twa-modal-overlay .stat-label { display: flex;\n            justify-content: space-between;\n            font-size: 12px;\n            margin-bottom: 4px; }\r\n#twa-modal-overlay .stat-progress { height: 8px;\n            background: #2a2a35;\n            border-radius: 4px;\n            overflow: hidden; }\r\n#twa-modal-overlay .stat-fill { height: 100%;\n            border-radius: 4px; }\r\n#twa-modal-overlay .fill-str { background: #f44336; }\r\n#twa-modal-overlay .fill-def { background: #2196f3; }\r\n#twa-modal-overlay .fill-spd { background: #ff9800; }\r\n#twa-modal-overlay .fill-dex { background: #9c27b0; }\r\n#twa-modal-overlay .ratio-box { background: #16161c;\n            border: 1px solid #2e2e38;\n            border-radius: 8px;\n            padding: 14px;\n            margin-top: 15px;\n            font-size: 13px; }\r\n#twa-modal-overlay .ratio-pill { display: inline-block;\n            background: #23232e;\n            padding: 4px 10px;\n            border-radius: 4px;\n            font-family: monospace;\n            font-weight: bold;\n            color: var(--accent-orange);\n            margin: 3px 0; }\r\n#twa-modal-overlay .goal-selector-card { background: #17171d;\n            border: 1px solid var(--border-color);\n            border-radius: 10px;\n            padding: 18px;\n            margin-bottom: 20px; }\r\n#twa-modal-overlay .goal-selector-card select { width: 100%;\n            padding: 11px 14px;\n            background: #23232c;\n            color: #fff;\n            border: 1px solid #444;\n            border-radius: 6px;\n            font-size: 14px;\n            outline: none;\n            margin-top: 8px;\n            cursor: pointer; }\r\n#twa-modal-overlay .goal-selector-card select:focus { border-color: var(--accent-orange); }\r\n#twa-modal-overlay .advice-box { background: var(--card-bg);\n            border: 1px solid var(--border-color);\n            border-radius: 10px;\n            padding: 20px;\n            margin-bottom: 20px; }\r\n#twa-modal-overlay .advice-box h3 { margin-top: 0;\n            color: var(--accent-orange);\n            font-size: 16px;\n            display: flex;\n            align-items: center;\n            gap: 8px; }\r\n#twa-modal-overlay .advice-item { margin-bottom: 12px;\n            padding-left: 15px;\n            border-left: 3px solid var(--accent-orange);\n            font-size: 13px;\n            line-height: 1.5; }\r\n#twa-modal-overlay .advice-item.warning { border-left-color: var(--accent-red); }\r\n#twa-modal-overlay .advice-item.success { border-left-color: var(--accent-green); }\r\n#twa-modal-overlay .advice-item strong { color: #fff; }\r\n#twa-modal-overlay .defend-explainer { background: #16161b;\n            padding: 14px;\n            border-radius: 6px;\n            font-size: 12px;\n            color: #b0b0c0;\n            line-height: 1.6;\n            margin-top: 10px;\n            border: 1px dashed #3a3a48; }\r\n#twa-modal-overlay .gear-grid { display: grid;\n            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));\n            gap: 10px;\n            margin-top: 10px; }\r\n#twa-modal-overlay .gear-item { background: #15151a;\n            border: 1px solid #2c2c36;\n            border-radius: 6px;\n            padding: 10px 12px;\n            font-size: 12px; }\r\n#twa-modal-overlay .gear-item.rarity-yellow { border-left: 3px solid #fdd835; }\r\n#twa-modal-overlay .gear-item.rarity-orange { border-left: 3px solid #fb8c00; }\r\n#twa-modal-overlay .gear-item.rarity-red { border-left: 3px solid #e53935; }\r\n#twa-modal-overlay .gear-name { font-weight: bold;\n            color: #fff;\n            font-size: 13px;\n            margin-bottom: 3px; }\r\n#twa-modal-overlay .gear-meta { color: var(--text-muted);\n            font-size: 11px;\n            line-height: 1.4; }\r\n#twa-modal-overlay .gear-bonus { color: var(--accent-orange);\n            font-weight: 500;\n            margin-top: 3px;\n            font-size: 11px; }\r\n#twa-modal-overlay .alert-banner { padding: 10px 14px;\n            border-radius: 6px;\n            font-size: 12px;\n            margin-bottom: 15px;\n            display: flex;\n            align-items: center;\n            gap: 10px; }\r\n#twa-modal-overlay .alert-warning { background: rgba(244, 67, 54, 0.15);\n            border: 1px solid var(--accent-red);\n            color: #ff8a80; }\r\n#twa-modal-overlay .alert-info { background: rgba(33, 150, 243, 0.15);\n            border: 1px solid var(--accent-blue);\n            color: #90caf9; }\r\n#twa-modal-overlay .loading-spinner { text-align: center;\n            padding: 40px;\n            font-size: 16px;\n            color: var(--text-muted);\n            display: none; }\r\n#twa-modal-overlay /* Modern Tabs Navigation */\n        .tab-bar { display: flex;\n            gap: 8px;\n            margin: 20px 0 16px 0;\n            border-bottom: 2px solid #2e2e38;\n            padding-bottom: 2px;\n            overflow-x: auto; }\r\n#twa-modal-overlay .tab-btn { background: #17171d;\n            border: 1px solid #33333f;\n            border-bottom: none;\n            color: #aaa;\n            padding: 10px 18px;\n            font-size: 13px;\n            font-weight: bold;\n            border-radius: 8px 8px 0 0;\n            cursor: pointer;\n            transition: all 0.2s ease;\n            white-space: nowrap;\n            display: flex;\n            align-items: center;\n            gap: 6px; }\r\n#twa-modal-overlay .tab-btn:hover { color: #fff;\n            background: #22222c; }\r\n#twa-modal-overlay .tab-btn.active { background: var(--card-bg);\n            color: var(--accent-orange);\n            border-color: var(--accent-orange);\n            border-bottom: 2px solid var(--card-bg);\n            margin-bottom: -2px; }\r\n#twa-modal-overlay .tab-content { display: none; }\r\n#twa-modal-overlay .tab-content.active { display: block; }\r\n";

    const TWA_EMBEDDED_HTML = "\u003cdiv class=\"container\"\u003e\n    \u003cdiv class=\"header\"\u003e\n        \u003ch1\u003e⚔️ Torn War Readiness \u0026 Combat Advisor\u003c/h1\u003e\n        \u003cp\u003e10/10 Ranked War Optimizer: Gear, Special Ammo, Merit Re-specs, Recent 100 Hits Audit \u0026 Baldr/Hank Ratios.\u003c/p\u003e\n    \u003c/div\u003e\n\n    \u003cdiv class=\"api-card\"\u003e\n        \u003cinput type=\"text\" id=\"apiKeyInput\" class=\"api-input\" placeholder=\"Paste 16-character Torn API Key...\" autocomplete=\"off\"\u003e\n        \u003cbutton id=\"analyzeBtn\" class=\"btn-analyze\" onclick=\"startAnalysis()\"\u003eAnalyze Combat Profile\u003c/button\u003e\n    \u003c/div\u003e\n\n    \u003cdiv id=\"loading\" class=\"loading-spinner\"\u003eAuditing war stats, API v2 equipment, special ammo, merits, and last 100 attacks...\u003c/div\u003e\n\n    \u003cdiv id=\"results\"\u003e\n        \u003c!-- Profile Overview Action Bar --\u003e\n        \u003cdiv style=\"display:flex; justify-content:space-between; align-items:center; margin-bottom:14px; gap:12px; flex-wrap:wrap;\"\u003e\n            \u003c!-- Quick Next Buy Recommendation Card --\u003e\n            \u003cdiv id=\"quickNextBuyCard\" style=\"flex:1; min-width:280px; background:#1b1b22; border:1px solid #363644; border-radius:8px; padding:10px 14px; display:flex; align-items:center; justify-content:space-between; gap:10px; box-shadow: 0 2px 8px rgba(0,0,0,0.3);\"\u003e\n                \u003cdiv style=\"display:flex; align-items:center; gap:10px;\"\u003e\n                    \u003cdiv style=\"font-size:22px;\" id=\"nextBuyIcon\"\u003e🛒\u003c/div\u003e\n                    \u003cdiv\u003e\n                        \u003cdiv style=\"font-size:11px; text-transform:uppercase; letter-spacing:0.5px; color:#9595a5;\"\u003eRecommended Next Buy\u003c/div\u003e\n                        \u003cdiv id=\"nextBuyTitle\" style=\"font-size:13px; font-weight:bold; color:#fff;\"\u003eAnalyzing profile...\u003c/div\u003e\n                    \u003c/div\u003e\n                \u003c/div\u003e\n                \u003cdiv id=\"nextBuyTag\" style=\"background:var(--accent-orange); color:#000; font-size:11px; font-weight:bold; padding:4px 10px; border-radius:20px; white-space:nowrap;\"\u003e\n                    Value: Evaluating\n                \u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Discord War Card Copy Button --\u003e\n            \u003cbutton onclick=\"copyDiscordSummary()\" style=\"background:#5865F2; color:#fff; border:none; padding:11px 18px; border-radius:8px; font-weight:bold; cursor:pointer; font-size:13px; height:100%; white-space:nowrap; display:flex; align-items:center; gap:6px;\"\u003e\n                📋 Copy War Card for Discord\n            \u003c/button\u003e\n        \u003c/div\u003e\n        \u003cdiv class=\"grid\"\u003e\n            \u003cdiv class=\"card\"\u003e\n                \u003cdiv class=\"card-title\"\u003eCombat Identity\u003c/div\u003e\n                \u003cdiv class=\"card-value\" id=\"playerName\"\u003e-\u003c/div\u003e\n                \u003cdiv id=\"archetypeBadge\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"card\"\u003e\n                \u003cdiv class=\"card-title\"\u003eTotal Battle Stats\u003c/div\u003e\n                \u003cdiv class=\"card-value\" id=\"totalStatsDisplay\" style=\"color: var(--accent-orange);\"\u003e-\u003c/div\u003e\n                \u003cdiv id=\"seThreshold\" style=\"font-size: 12px; color: var(--text-muted);\"\u003eSE Threshold: Calculating...\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"card\"\u003e\n                \u003cdiv class=\"card-title\"\u003eCareer Defend Rate\u003c/div\u003e\n                \u003cdiv class=\"card-value\" id=\"defendRateDisplay\"\u003e-\u003c/div\u003e\n                \u003cdiv id=\"defendStatus\" style=\"font-size: 12px;\"\u003eEvaluating...\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"card\"\u003e\n                \u003cdiv class=\"card-title\"\u003eRecent 100 War Hits\u003c/div\u003e\n                \u003cdiv class=\"card-value\" id=\"recentWarRateDisplay\" style=\"color: var(--accent-blue);\"\u003e-\u003c/div\u003e\n                \u003cdiv id=\"recentWarStatus\" style=\"font-size: 12px;\"\u003eAnalyzing attacks...\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- Career Defend Explainer Box --\u003e\n        \u003cdiv class=\"card\" style=\"margin-bottom: 20px;\"\u003e\n            \u003cdiv class=\"card-title\"\u003eDefend Win Rate Breakdown \u0026 Explanation\u003c/div\u003e\n            \u003cdiv class=\"defend-explainer\" id=\"defendExplainerText\"\u003e\n                Loading career defend analysis...\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- Stat Distribution \u0026 Current Ratio Card --\u003e\n        \u003cdiv class=\"card\" style=\"margin-bottom: 20px;\"\u003e\n            \u003cdiv class=\"card-title\"\u003eCurrent Battle Stats \u0026 Ratio Breakdown\u003c/div\u003e\n            \u003cdiv class=\"stat-bar-container\"\u003e\n                \u003cdiv class=\"stat-label\"\u003e\u003cspan\u003eStrength\u003c/span\u003e\u003cb id=\"strVal\"\u003e-\u003c/b\u003e\u003c/div\u003e\n                \u003cdiv class=\"stat-progress\"\u003e\u003cdiv id=\"strBar\" class=\"stat-fill fill-str\" style=\"width: 25%\"\u003e\u003c/div\u003e\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"stat-bar-container\"\u003e\n                \u003cdiv class=\"stat-label\"\u003e\u003cspan\u003eDefense\u003c/span\u003e\u003cb id=\"defVal\"\u003e-\u003c/b\u003e\u003c/div\u003e\n                \u003cdiv class=\"stat-progress\"\u003e\u003cdiv id=\"defBar\" class=\"stat-fill fill-def\" style=\"width: 25%\"\u003e\u003c/div\u003e\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"stat-bar-container\"\u003e\n                \u003cdiv class=\"stat-label\"\u003e\u003cspan\u003eSpeed\u003c/span\u003e\u003cb id=\"spdVal\"\u003e-\u003c/b\u003e\u003c/div\u003e\n                \u003cdiv class=\"stat-progress\"\u003e\u003cdiv id=\"spdBar\" class=\"stat-fill fill-spd\" style=\"width: 25%\"\u003e\u003c/div\u003e\u003c/div\u003e\n            \u003c/div\u003e\n            \u003cdiv class=\"stat-bar-container\"\u003e\n                \u003cdiv class=\"stat-label\"\u003e\u003cspan\u003eDexterity\u003c/span\u003e\u003cb id=\"dexVal\"\u003e-\u003c/b\u003e\u003c/div\u003e\n                \u003cdiv class=\"stat-progress\"\u003e\u003cdiv id=\"dexBar\" class=\"stat-fill fill-dex\" style=\"width: 25%\"\u003e\u003c/div\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003cdiv class=\"ratio-box\" id=\"currentRatioBox\"\u003e\n                Loading ratio analysis...\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- 5-Tab Navigation System for Clean Readability --\u003e\n        \u003cdiv class=\"tab-bar\"\u003e\n            \u003cbutton class=\"tab-btn active\" id=\"tabBtn-war\" onclick=\"switchTwaTab(\u0027war\u0027)\"\u003e⚔️ War \u0026 Targets\u003c/button\u003e\n            \u003cbutton class=\"tab-btn\" id=\"tabBtn-gym\" onclick=\"switchTwaTab(\u0027gym\u0027)\"\u003e🏋️ Gym \u0026 Build (GymIQ)\u003c/button\u003e\n            \u003cbutton class=\"tab-btn\" id=\"tabBtn-gear\" onclick=\"switchTwaTab(\u0027gear\u0027)\"\u003e🎒 Gear \u0026 Loadout\u003c/button\u003e\n            \u003cbutton class=\"tab-btn\" id=\"tabBtn-merits\" onclick=\"switchTwaTab(\u0027merits\u0027)\"\u003e🎖️ Merits \u0026 Discipline\u003c/button\u003e\n            \u003cbutton class=\"tab-btn\" id=\"tabBtn-economy\" onclick=\"switchTwaTab(\u0027economy\u0027)\"\u003e💰 Economy \u0026 Networth\u003c/button\u003e\n        \u003c/div\u003e\n\n        \u003c!-- ==================== TAB 1: WAR \u0026 TARGETS ==================== --\u003e\n        \u003cdiv id=\"tab-war\" class=\"tab-content active\"\u003e\n            \u003c!-- Active War Targets \u0026 Doable FF Roster Scanner --\u003e\n            \u003cdiv class=\"advice-box\" style=\"border-color: var(--accent-orange); background: #13131a;\"\u003e\n                \u003cdiv style=\"display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:10px;\"\u003e\n                    \u003cdiv\u003e\n                        \u003ch3 style=\"margin:0; color:var(--accent-orange);\"\u003e⚔️ Active Ranked War Target Scanner (Best Fair Fight \u0026 Respect Farm)\u003c/h3\u003e\n                        \u003cdiv style=\"font-size:12px; color:#aaa; margin-top:4px;\"\u003e\n                            Scans the enemy faction roster in your active Ranked War, computes predicted stats via BSP, and sorts targets by \u003cstrong\u003eOptimal Fair Fight (FF) \u0026 Respect Yield\u003c/strong\u003e.\n                        \u003c/div\u003e\n                    \u003c/div\u003e\n                    \u003cbutton onclick=\"scanActiveWarRoster()\" class=\"btn-analyze\" style=\"background:var(--accent-orange); color:#000; font-weight:bold; padding:8px 18px; border-radius:6px; cursor:pointer;\"\u003e\n                        🔄 Scan War Targets\n                    \u003c/button\u003e\n                \u003c/div\u003e\n                \u003cdiv id=\"warScanStatus\" style=\"margin-top:12px; font-size:12px; color:#ccc;\"\u003e\n                    Click \u003cstrong\u003e\"Scan War Targets\"\u003c/strong\u003e to detect your active war and rank enemy faction members by farmability.\n                \u003c/div\u003e\n                \u003cdiv id=\"warRosterContainer\" style=\"margin-top:12px; overflow-x:auto;\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Enemy Target Spy / Quick Compare --\u003e\n            \u003cdiv class=\"advice-box\" style=\"border-color: var(--accent-blue);\"\u003e\n                \u003cdiv style=\"display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;\"\u003e\n                    \u003ch3 style=\"margin:0;\"\u003e🎯 Enemy Target Scout (BSP Stat Predictor Engine)\u003c/h3\u003e\n                    \u003cdiv style=\"display:flex; gap:6px;\"\u003e\n                        \u003cspan id=\"bspBridgeBadge\" class=\"badge\" style=\"background:#222; color:#aaa; border:1px solid #444; font-size:10px;\"\u003eChecking BSP Bridge...\u003c/span\u003e\n                        \u003cspan class=\"badge\" style=\"background:var(--accent-orange); color:#000; font-size:10px;\"\u003eTDup Machine Model\u003c/span\u003e\n                    \u003c/div\u003e\n                \u003c/div\u003e\n                \u003cp style=\"font-size:12px; color:#aaa; margin:6px 0 12px 0;\"\u003e\n                    Enter an enemy Torn User ID to calculate or pull their \u003cstrong\u003ePredicted Total Battle Stats (TBS)\u003c/strong\u003e directly from your installed BSP userscript cache or mathematical energy model:\n                \u003c/p\u003e\n                \u003cdiv style=\"display:flex; gap:10px; margin-bottom:8px;\"\u003e\n                    \u003cinput type=\"text\" id=\"enemyIdInput\" class=\"api-input\" placeholder=\"Enemy Torn ID (e.g. 2899019)\" style=\"padding:10px 14px; flex:1;\"\u003e\n                    \u003cbutton onclick=\"scoutEnemy()\" class=\"btn-analyze\" style=\"background:var(--accent-blue); color:#fff; padding:10px 20px;\"\u003eScout \u0026 Predict Stats\u003c/button\u003e\n                \u003c/div\u003e\n                \u003cdiv id=\"bspBridgeStatus\" style=\"font-size:11px; color:#888; margin-bottom:8px;\"\u003e\u003c/div\u003e\n                \u003cdiv id=\"enemyScoutResult\" style=\"font-size:13px; color:#ddd;\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- ==================== TAB 2: GYM \u0026 BUILD (GYMIQ) ==================== --\u003e\n        \u003cdiv id=\"tab-gym\" class=\"tab-content\"\u003e\n            \u003c!-- Target Goal Selector with GymIQ Standards --\u003e\n            \u003cdiv class=\"goal-selector-card\"\u003e\n                \u003cdiv style=\"display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;\"\u003e\n                    \u003cdiv style=\"font-weight: bold; font-size: 14px; color: var(--accent-orange);\"\u003e🎯 Select Your Combat \u0026 Gym Target Ratio:\u003c/div\u003e\n                    \u003cspan class=\"badge\" style=\"background:#ff9900; color:#000; font-size:10px;\"\u003e⚡ Powered by GymIQ Standards\u003c/span\u003e\n                \u003c/div\u003e\n                \u003cdiv style=\"font-size: 12px; color: var(--text-muted); margin-top: 4px;\"\u003e\n                    Calculates exact stat targets, deficits, gym unlocks, and live \u003cstrong\u003eGymIQ \"Train Next\"\u003c/strong\u003e recommendations:\n                \u003c/div\u003e\n                \u003cselect id=\"goalSelector\" onchange=\"updateGoalRoadmap()\"\u003e\n                    \u003coptgroup label=\"⚖️ Baldr\u0027s Specialist \u0026 Whore Ratios (100 Primary : 80 Secondary : 72 : 72)\"\u003e\n                        \u003coption value=\"baldr_dex\"\u003e💨 Baldr\u0027s Dex Whore (100 Dex : 80 Spd : 72 Str : 72 Def — The Elites + Frontline)\u003c/option\u003e\n                        \u003coption value=\"baldr_def\"\u003e🛡️ Baldr\u0027s Defense Tank (100 Def : 80 Str : 72 Spd : 72 Dex — Isoyama + Frontline)\u003c/option\u003e\n                        \u003coption value=\"baldr_str\"\u003e🏋️ Baldr\u0027s Strength Whore (100 Str : 80 Def : 72 Dex : 72 Spd — Gym 3000 + Balboa\u0027s)\u003c/option\u003e\n                        \u003coption value=\"baldr_spd\"\u003e⚡ Baldr\u0027s Speed Whore (100 Spd : 80 Def : 72 Dex : 72 Str — Total Rebound + Balboa\u0027s)\u003c/option\u003e\n                        \u003coption value=\"baldr_ratio\"\u003e⚖️ Baldr\u0027s Auto-Balanced (Auto-Pick Top 2 Stats as Primary \u0026 Secondary)\u003c/option\u003e\n                        \u003coption value=\"pure_balanced\"\u003e🛡️ Pure Balanced Titan (1 : 1 : 1 : 1 — Equal 25% All Stats)\u003c/option\u003e\n                    \u003c/optgroup\u003e\n                    \u003coptgroup label=\"🔥 Hank\u0027s Extreme Specialist Ratios (100 : 80 : 80 : 28 Max Dump)\"\u003e\n                        \u003coption value=\"hank_dex\"\u003e💨 Hank\u0027s Dex Whore (100 Dex : 80 Spd : 80 Str : 28 Def Dump)\u003c/option\u003e\n                        \u003coption value=\"hank_def\"\u003e🛡️ Hank\u0027s Defense Tank (100 Def : 80 Str : 80 Spd : 28 Dex Dump)\u003c/option\u003e\n                        \u003coption value=\"hank_str\"\u003e🏋️ Hank\u0027s Strength Whore (100 Str : 80 Def : 80 Dex : 28 Spd Dump)\u003c/option\u003e\n                        \u003coption value=\"hank_spd\"\u003e⚡ Hank\u0027s Speed Demon (100 Spd : 80 Def : 80 Dex : 28 Str Dump)\u003c/option\u003e\n                    \u003c/optgroup\u003e\n                    \u003coptgroup label=\"🚀 GymIQ Late-Game Scaling\"\u003e\n                        \u003coption value=\"se_rush\"\u003e🚀 Stat Enhancer (SE) Rush: Fast-Track 1.5B in Highest Stat\u003c/option\u003e\n                    \u003c/optgroup\u003e\n                \u003c/select\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Target Goal Strategy Roadmap --\u003e\n            \u003cdiv class=\"advice-box\" id=\"goalRoadmapBox\" style=\"border: 2px solid var(--accent-orange);\"\u003e\n                \u003ch3 id=\"goalTitle\"\u003e🎯 Calculated Gym Ratio \u0026 Roadmap\u003c/h3\u003e\n                \u003cdiv id=\"gymUnlockBanner\"\u003e\u003c/div\u003e\n                \u003cdiv id=\"goalAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Training Progression Economy: Happy Jumps vs Energy Cans vs Stat Enhancers (SE) --\u003e\n            \u003cdiv class=\"advice-box\" style=\"border: 2px solid var(--accent-orange); background: #15151c;\"\u003e\n                \u003cdiv style=\"display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;\"\u003e\n                    \u003ch3 style=\"margin:0; color:var(--accent-orange);\"\u003e⚡ Training Progression Economy: Happy Jumps, Energy Cans \u0026 Stat Enhancers\u003c/h3\u003e\n                    \u003cspan class=\"badge\" style=\"background:#ff9900; color:#000;\"\u003eFull Lifetime ROI Engine\u003c/span\u003e\n                \u003c/div\u003e\n                \u003cp style=\"font-size:12px; color:var(--text-muted); margin:8px 0 14px 0;\"\u003e\n                    Tailored for every stage of your Torn journey: Early-game \u003cstrong\u003eHappy Jumps (\u0026lt;400k-800k threshold)\u003c/strong\u003e, Mid-game \u003cstrong\u003e3x Xanax + Energy Cans (to 1.5B)\u003c/strong\u003e, and Late-game \u003cstrong\u003eStat Enhancers (1.5B+)\u003c/strong\u003e with exact cost-per-stat ($/gain) comparisons.\n                \u003c/p\u003e\n                \u003cdiv id=\"boosterRoiContainer\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- ==================== TAB 3: GEAR \u0026 LOADOUT ==================== --\u003e\n        \u003cdiv id=\"tab-gear\" class=\"tab-content\"\u003e\n            \u003c!-- Equipped Gear \u0026 Ammo Audit Card --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🎒 Equipped Arsenal \u0026 Special Ammo (Live from API v2)\u003c/h3\u003e\n                \u003cdiv id=\"ammoAlertContainer\"\u003e\u003c/div\u003e\n                \u003cdiv id=\"equippedGearGrid\" class=\"gear-grid\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Weapons Advice --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🎯 Weapon Mastery \u0026 Loadout Audit\u003c/h3\u003e\n                \u003cdiv id=\"weaponAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- General War \u0026 Defensive Advice --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🛡️ Armor \u0026 Next-Step Gear Upgrades\u003c/h3\u003e\n                \u003cdiv id=\"defenseAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- ==================== TAB 4: MERITS \u0026 DISCIPLINE ==================== --\u003e\n        \u003cdiv id=\"tab-merits\" class=\"tab-content\"\u003e\n            \u003c!-- Recent Attacks \u0026 Chaining Audit --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e⚔️ Recent 100 Attacks \u0026 Chaining Analysis\u003c/h3\u003e\n                \u003cdiv id=\"recentAttacksAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Veteran War Prep \u0026 Discipline --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🎖️ Veteran War Prep \u0026 Discipline (Revives, Xanax \u0026 Hit Types)\u003c/h3\u003e\n                \u003cdiv id=\"veteranPrepAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Education \u0026 Gym Courses Checklist --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🎓 Mandatory Combat \u0026 Gym Education Checklist\u003c/h3\u003e\n                \u003cdiv id=\"educationAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n\n            \u003c!-- Merits \u0026 Perk Optimization --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e✨ Merits \u0026 Combat Perks Audit\u003c/h3\u003e\n                \u003cdiv id=\"meritsAdvice\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n\n        \u003c!-- ==================== TAB 5: ECONOMY \u0026 NETWORTH ==================== --\u003e\n        \u003cdiv id=\"tab-economy\" class=\"tab-content\"\u003e\n            \u003c!-- Networth, Stocks \u0026 Inventory Valuation --\u003e\n            \u003cdiv class=\"advice-box\"\u003e\n                \u003ch3\u003e🏦 Networth, Stocks \u0026 Inventory Valuation\u003c/h3\u003e\n                \u003cdiv id=\"networthBreakdown\"\u003e\u003c/div\u003e\n            \u003c/div\u003e\n        \u003c/div\u003e\n    \u003c/div\u003e\n\u003c/div\u003e";

    function initAdvisorLogic() {
        console.log('[TornWarAdvisor] Initializing advisor dashboard logic...');

        // Universal GM_xmlhttpRequest fetcher for both Torn API & TDup lol-manager
        const twaFetch = function(url) {
            return new Promise((resolve, reject) => {
                console.log('[TornWarAdvisor] Requesting via GM_xmlhttpRequest:', url);
                GM_xmlhttpRequest({
                    method: 'GET',
                    url: url,
                    headers: { 'Accept': 'application/json' },
                    onload: function(res) {
                        try {
                            const data = JSON.parse(res.responseText);
                            if (data && data.error) {
                                reject(new Error(data.error.error || JSON.stringify(data.error)));
                            } else {
                                resolve(data);
                            }
                        } catch (e) {
                            reject(new Error('JSON parse error: ' + res.responseText));
                        }
                    },
                    onerror: function(err) {
                        console.error('[TornWarAdvisor] GM_xmlhttpRequest failed, trying direct fetch:', err);
                        fetch(url)
                            .then(r => r.json())
                            .then(resolve)
                            .catch(e => reject(err));
                    }
                });
            });
        };

        window.fetchWithFallback = twaFetch;
        targetWindow.fetchWithFallback = twaFetch;

        // Direct GM_xmlhttpRequest helper for TDup lol-manager BSP API
        const twaBsp = function(apiKey, targetId) {
            return new Promise((resolve, reject) => {
                if (!apiKey || !targetId) {
                    return resolve(null);
                }
                console.log('[TornWarAdvisor] Requesting BSP stats via GM_xmlhttpRequest for target:', targetId);
                GM_xmlhttpRequest({
                    method: 'GET',
                    url: 'https://www.lol-manager.com/api/battlestats/' + encodeURIComponent(apiKey) + '/' + encodeURIComponent(targetId) + '/1.0',
                    headers: { 'Accept': 'application/json' },
                    onload: function(res) {
                        try {
                            const data = JSON.parse(res.responseText);
                            console.log('[TornWarAdvisor] BSP response received:', data);
                            resolve(data);
                        } catch (e) {
                            console.error('[TornWarAdvisor] Failed to parse BSP response:', res.responseText);
                            resolve(null);
                        }
                    },
                    onerror: function(err) {
                        console.error('[TornWarAdvisor] GM_xmlhttpRequest BSP error:', err);
                        resolve(null);
                    }
                });
            });
        };

        const requestBspFromExtension = twaBsp;
        const requestBspDirect = twaBsp;
        window.requestBspDirect = twaBsp;
        window.requestBspFromExtension = twaBsp;
        targetWindow.requestBspDirect = twaBsp;
        targetWindow.requestBspFromExtension = twaBsp;

        // Auto-load saved Torn API key from GM_getValue / localStorage
        const savedKey = (typeof GM_getValue !== 'undefined' ? GM_getValue('twa_torn_key', '') : '') || localStorage.getItem('twa_torn_key') || localStorage.getItem('torn_api_key') || '';
        const keyInput = document.getElementById('apiKeyInput');
        if (keyInput && savedKey) {
            keyInput.value = savedKey;
        }

        // Bridge badge in userscript is always DIRECTLY CONNECTED
        const badge = document.getElementById('bspBridgeBadge');
        if (badge) {
            badge.innerText = '⚡ Official BSP API Direct';
            badge.style.background = 'var(--accent-green)';
            badge.style.color = '#000';
        }
        const bspStatus = document.getElementById('bspBridgeStatus');
        if (bspStatus) {
            bspStatus.innerHTML = '<span style=\"color:var(--accent-green);\">⚡ All-in-One Userscript connected directly to TDup BSP API (GM_xmlhttpRequest active).</span>';
        }

        // Save key on change or input
        if (keyInput) {
            const saveHandler = () => {
                const val = keyInput.value.trim();
                if (typeof GM_setValue !== 'undefined') GM_setValue('twa_torn_key', val);
                localStorage.setItem('twa_torn_key', val);
                localStorage.setItem('torn_api_key', val);
            };
            keyInput.addEventListener('change', saveHandler);
            keyInput.addEventListener('input', saveHandler);
        }
// Tab switching function
    function switchTwaTab(tabKey) {
        // Remove active class from all buttons and tabs
        document.querySelectorAll('.tab-btn').forEach(btn => btn.classList.remove('active'));
        document.querySelectorAll('.tab-content').forEach(tab => tab.classList.remove('active'));

        const targetBtn = document.getElementById('tabBtn-' + tabKey);
        const targetTab = document.getElementById('tab-' + tabKey);

        if (targetBtn) targetBtn.classList.add('active');
        if (targetTab) targetTab.classList.add('active');
    }
    window.switchTwaTab = switchTwaTab;

    let isBspBridgeActive = false;

    // Native userscript uses direct GM_xmlhttpRequest

    // Check if any bridge data is already stored
    window.addEventListener('DOMContentLoaded', () => {
        const badge = document.getElementById('bspBridgeBadge');
        if (badge) {
            badge.style.background = 'var(--accent-green)';
            badge.style.color = '#000';
            badge.innerText = '⚡ BSP Bridge Ready';
        }
    });

    let globalPlayerData = null;
    let globalEquipment = [];
    let globalAttacks = {};
    let globalRecentXan = null;
    let globalItemPrices = {};

    const formatNumber = (num) => Number(num).toLocaleString('en-US');
    const formatShortNumber = (num) => {
        if (num >= 1e9) return (num / 1e9).toFixed(2) + 'B';
        if (num >= 1e6) return (num / 1e6).toFixed(2) + 'M';
        if (num >= 1e3) return (num / 1e3).toFixed(1) + 'K';
        return num.toString();
    };

    // fetchWithFallback handled by GM_xmlhttpRequest

    // requestBspFromExtension handled by GM_xmlhttpRequest twaBsp

    async function startAnalysis() {
        const key = document.getElementById('apiKeyInput').value.trim();
        if (!key) {
            alert('Please enter a valid Torn API key!');
            return;
        }

        document.getElementById('loading').style.display = 'block';
        document.getElementById('results').style.display = 'none';

        try {
            const nowSec = Math.floor(Date.now() / 1000);
            const thirtyDaysAgo = nowSec - (30 * 86400);

            const v1Url = `https://api.torn.com/user/?selections=profile,battlestats,personalstats,weaponexp,merits,perks,attacks,education,networth&key=${key}`;
            const v2EquipUrl = `https://api.torn.com/v2/user/equipment?key=${key}`;
            const histXanUrl = `https://api.torn.com/user/?selections=personalstats&timestamp=${thirtyDaysAgo}&stat=xantaken&key=${key}`;
            const marketItemsUrl = `https://api.torn.com/torn/530,532,533,553,554,555,985,986,987,367,206,366,197,330,331,329,106?selections=items&key=${key}`;

            const [data, equipData, histData, marketItemsData] = await Promise.all([
                fetchWithFallback(v1Url),
                fetchWithFallback(v2EquipUrl).catch(() => ({ equipment: [] })),
                fetchWithFallback(histXanUrl).catch(() => ({ personalstats: {} })),
                fetchWithFallback(marketItemsUrl).catch(() => ({ items: {} }))
            ]);

            if (data.error) {
                alert('API Error: ' + (data.error.error || JSON.stringify(data.error)));
                document.getElementById('loading').style.display = 'none';
                return;
            }

            globalPlayerData = data;
            globalEquipment = equipData.equipment || [];
            globalItemPrices = marketItemsData.items || {};
            
            // Maintain persistent cached combat history across sessions
            let cachedArchive = {};
            try {
                const rawCache = localStorage.getItem('twa_combat_history_archive');
                if (rawCache) cachedArchive = JSON.parse(rawCache);
            } catch (e) {}

            globalAttacks = Object.assign({}, cachedArchive, data.attacks || {});
            try {
                localStorage.setItem('twa_combat_history_archive', JSON.stringify(globalAttacks));
            } catch (e) {}

            globalRecentXan = histData?.personalstats?.xantaken !== undefined ? histData.personalstats.xantaken : null;

            renderCombatReport(data, globalEquipment, globalAttacks, globalRecentXan);
            updateGoalRoadmap();

            document.getElementById('loading').style.display = 'none';
            document.getElementById('results').style.display = 'block';
        } catch (err) {
            console.error(err);
            alert('Failed to connect to Torn API: ' + (err.message || err));
            document.getElementById('loading').style.display = 'none';
        }
    }

    function renderCombatReport(d, equipment, attacks, pastMonthXan) {
        const name = d.name || 'Soldier';
        const lvl = d.level || 0;
        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const pstats = d.personalstats || {};

        const str = bs.strength || 0;
        const def = bs.defense || 0;
        const spd = bs.speed || 0;
        const dex = bs.dexterity || 0;
        const total = str + def + spd + dex;

        document.getElementById('playerName').innerText = `${name} [Lvl ${lvl}]`;
        document.getElementById('totalStatsDisplay').innerText = formatShortNumber(total);

        const maxStat = Math.max(str, def, spd, dex, 1);
        const minStat = Math.max(1, Math.min(str, def, spd, dex));
        document.getElementById('strVal').innerText = `${formatShortNumber(str)} (${Math.round((str/total)*100)}%)`;
        document.getElementById('defVal').innerText = `${formatShortNumber(def)} (${Math.round((def/total)*100)}%)`;
        document.getElementById('spdVal').innerText = `${formatShortNumber(spd)} (${Math.round((spd/total)*100)}%)`;
        document.getElementById('dexVal').innerText = `${formatShortNumber(dex)} (${Math.round((dex/total)*100)}%)`;

        document.getElementById('strBar').style.width = `${(str / maxStat) * 100}%`;
        document.getElementById('defBar').style.width = `${(def / maxStat) * 100}%`;
        document.getElementById('spdBar').style.width = `${(spd / maxStat) * 100}%`;
        document.getElementById('dexBar').style.width = `${(dex / maxStat) * 100}%`;

        // Normalized Ratio
        const rStr = (str / minStat).toFixed(2);
        const rDef = (def / minStat).toFixed(2);
        const rSpd = (spd / minStat).toFixed(2);
        const rDex = (dex / minStat).toFixed(2);

        document.getElementById('currentRatioBox').innerHTML = `
            <strong>Your Current Normalized Ratio:</strong><br>
            <span class="ratio-pill">${rStr} Str : ${rDef} Def : ${rSpd} Spd : ${rDex} Dex</span><br>
            <span style="color:#aaa; font-size:12px;">
                Percentage Shares: <b>${((str/total)*100).toFixed(1)}% Str</b> | <b>${((def/total)*100).toFixed(1)}% Def</b> | <b>${((spd/total)*100).toFixed(1)}% Spd</b> | <b>${((dex/total)*100).toFixed(1)}% Dex</b>
            </span>
        `;

        // Career Defend Stats
        const defWon = pstats.defendswon || 0;
        const defLost = pstats.defendslost || 0;
        const totalDef = defWon + defLost;
        const defRate = totalDef > 0 ? ((defWon / totalDef) * 100).toFixed(1) : 0;
        document.getElementById('defendRateDisplay').innerText = `${defRate}%`;
        
        const defStatusEl = document.getElementById('defendStatus');
        const explainerEl = document.getElementById('defendExplainerText');

        let explainerHtml = `<strong>Total Career Defends:</strong> ${formatNumber(totalDef)} (${formatNumber(defWon)} Won / ${formatNumber(defLost)} Lost)<br><br>`;

        if (defRate < 10) {
            defStatusEl.innerText = '⚠️ Severe Defensive Liability (Pinata)';
            defStatusEl.style.color = 'var(--accent-red)';
            explainerHtml += `
                <strong>Why is your defend rate so low (${defRate}%)?</strong><br>
                In Torn, attackers enjoy massive advantages: they shoot first, throw temps (Pepper Spray, Flash, Smoke), and pick targets they know they can beat. 
                A rate below 10% indicates you are a <em>ranked war target</em>: opponents exploit low defense or counterable stats and chain you for easy respect.
            `;
        } else if (defRate < 30) {
            defStatusEl.innerText = 'Moderate Defensive Hold';
            defStatusEl.style.color = 'var(--accent-orange)';
            explainerHtml += `<strong>Normal War Range:</strong> Winning 10%–30% is standard. You beat equal opponents, but higher stats and group attacks break through.`;
        } else {
            defStatusEl.innerText = '🛡️ High Defensive Fortification';
            defStatusEl.style.color = 'var(--accent-green)';
            explainerHtml += `<strong>Elite Defense:</strong> Over 30% defend wins is exceptional. You frequently waste enemy war energy and break chains.`;
        }
        explainerEl.innerHTML = explainerHtml;

        // Recent 100 Attacks Audit
        auditRecentAttacks(attacks, d.player_id);

        // Archetype
        let archetype = 'Balanced Fighter';
        let badgeClass = 'badge-balanced';
        if (dex > total * 0.35 && def < total * 0.20) {
            archetype = 'Evasive Rogue (Dex Whore)';
            badgeClass = 'badge-rogue';
        } else if (def > total * 0.40) {
            archetype = 'Hank Tank (Defense Wall)';
            badgeClass = 'badge-tank';
        } else if (spd > total * 0.35 && str > total * 0.35) {
            archetype = 'Glass Cannon (Speed/Strength)';
            badgeClass = 'badge-glass';
        } else if (str > total * 0.40) {
            archetype = 'Strength Whore';
            badgeClass = 'badge-glass';
        }
        document.getElementById('archetypeBadge').innerHTML = `<span class="badge ${badgeClass}">${archetype}</span>`;

        // SE Threshold
        const highestStat = Math.max(str, def, spd, dex);
        const seTarget = 1.5e9;
        const seEl = document.getElementById('seThreshold');
        if (highestStat >= seTarget) {
            const gainPerSE = formatShortNumber(highestStat * 0.01);
            seEl.innerHTML = `<b style="color:var(--accent-green);">Ready for Stat Enhancers!</b> (+${gainPerSE} per SE)`;
        } else {
            seEl.innerText = `${formatShortNumber(seTarget - highestStat)} more stats in highest stat for SEs.`;
        }

        // Render Quick Next Buy Card
        updateQuickNextBuy(d, equipment);

        // Render Equipment & Ammo Audit
        renderEquipmentAndAmmo(equipment);

        // Armor & Weapons Advice
        renderGearAdvice(equipment, d);

        // Merits & Perks Audit (Pass equipment for weapon mastery match)
        auditMeritsAndPerks(d, equipment);

        // Veteran War Prep & Discipline
        auditVeteranPrep(d, attacks, pastMonthXan);

        // Education Checklist
        auditEducation(d);

        // Booster & SE ROI Engine
        auditBoosterAndSeRoi(d);

        // Networth, Stocks & Inventory
        auditNetworth(d);
    }

    function updateQuickNextBuy(d, equipment) {
        const titleEl = document.getElementById('nextBuyTitle');
        const tagEl = document.getElementById('nextBuyTag');
        const iconEl = document.getElementById('nextBuyIcon');
        if (!titleEl || !tagEl || !iconEl) return;

        const goal = document.getElementById('goalSelector')?.value || 'hank_dex';
        const nw = d.networth || {};
        const bank = nw.bank || 0;
        const stocks = nw.stockmarket || 0;
        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const str = bs.strength || 0;
        const def = bs.defense || 0;
        const spd = bs.speed || 0;
        const dex = bs.dexterity || 0;
        const highestStat = Math.max(str, def, spd, dex);
        const dominantName = str >= Math.max(def, spd, dex) ? 'Strength' :
                             def >= Math.max(spd, dex) ? 'Defense' :
                             spd >= dex ? 'Speed' : 'Dexterity';

        const isDexBuild = (goal === 'hank_dex' || goal === 'baldr_dex' || (goal === 'baldr_ratio' && dominantName === 'Dexterity'));

        const armorPieces = (equipment || []).filter(e => e.type === 'Armor');
        const hasRiotHelmet = armorPieces.some(a => a.name.includes('Riot Helmet'));

        // Filter out Riot Helmet when evaluating weakest piece to upgrade for Dex builds!
        const upgradablePieces = armorPieces.filter(a => !(isDexBuild && a.name.includes('Riot Helmet')));
        const weakestArmor = [...upgradablePieces].sort((a, b) => (a.stats?.armor || 0) - (b.stats?.armor || 0))[0];

        // 1. Priority #1: Max $2B City Bank
        if (bank < 2000000000) {
            const needed = 2000000000 - bank;
            iconEl.innerText = '🏦';
            titleEl.innerHTML = `Fill City Bank ($${formatShortNumber(bank)} / $2B)`;
            tagEl.innerText = `~$${formatShortNumber(needed)} to Cap • Top ROI`;
            tagEl.style.background = 'var(--accent-orange)';
            tagEl.style.color = '#000';
            return;
        }

        // 2. Priority #2: Tactical Riot Helmet for Dex builds (if missing)
        if (!hasRiotHelmet && isDexBuild) {
            iconEl.innerText = '🪖';
            titleEl.innerHTML = `Tactical Riot Helmet (Anti-Spray)`;
            tagEl.innerText = `~$15M • Essential Survival`;
            tagEl.style.background = 'var(--accent-green)';
            tagEl.style.color = '#000';
            return;
        }

        // 3. Priority #3: Key Stock Block (SYM / TCI / FHG)
        if (stocks < 1000000000) {
            iconEl.innerText = '📈';
            titleEl.innerHTML = `SYM Stock Block (Symbiotic Drug Pack)`;
            tagEl.innerText = `~$130M • +18% Passive ROI`;
            tagEl.style.background = 'var(--accent-green)';
            tagEl.style.color = '#000';
            return;
        }

        // 4. Priority #4: Weakest Non-Riot Armor Slot Upgrade
        if (weakestArmor && (weakestArmor.stats?.armor || 0) < 44) {
            iconEl.innerText = '🛡️';
            titleEl.innerHTML = `Upgrade ${weakestArmor.name} → Assault (45+)`;
            tagEl.innerText = `~$25M–$45M • War Armor`;
            tagEl.style.background = 'var(--accent-blue)';
            tagEl.style.color = '#fff';
            return;
        }

        // 5. Priority #5: Stat Enhancers (if >= 1.5B)
        if (highestStat >= 1.5e9) {
            const seItemMap = { 'Strength': 'Boxing Gloves', 'Defense': 'Dumbbells', 'Speed': 'Skateboard', 'Dexterity': 'Parachute' };
            const seItem = seItemMap[dominantName] || 'Stat Enhancer';
            iconEl.innerText = '💊';
            titleEl.innerHTML = `${seItem} (+1% to ${dominantName})`;
            tagEl.innerText = `~$460M • +${formatShortNumber(highestStat * 0.01)} Stats`;
            tagEl.style.background = 'var(--accent-purple)';
            tagEl.style.color = '#fff';
            return;
        }

        // 6. Priority #6: Heavy Training Booster Pack (20e/25e Energy Cans)
        iconEl.innerText = '⚡';
        titleEl.innerHTML = `Bulk Energy Cans (Munster / Mactear)`;
        tagEl.innerText = `~$3M/ea • Faster 1.5B SE Rush`;
        tagEl.style.background = 'var(--accent-orange)';
        tagEl.style.color = '#000';
    }

    function auditNetworth(d) {
        const nw = d.networth || {};
        const container = document.getElementById('networthBreakdown');
        if (!container) return;

        const total = nw.total || 0;
        const stocks = nw.stockmarket || 0;
        const bank = nw.bank || 0;
        const items = nw.items || 0;
        const wallet = nw.wallet || 0;
        const points = nw.points || 0;

        let nwAdvice = '';
        if (bank < 2000000000) {
            nwAdvice += `<div class="advice-item warning"><strong>Priority #1 — Max City Bank ($2B):</strong> You currently have $${Math.round(bank).toLocaleString('en-US')} in the City Bank. Maxing out the $2,000,000,000 cap on 3-month cycles (with 10/10 Bank Merits + TCI stock) is the highest guaranteed ROI in Torn.</div>`;
        } else {
            nwAdvice += `<div class="advice-item success"><strong>✅ City Bank Maxed ($2B+):</strong> Your bank investment is maxed out and compounding passively.</div>`;
        }

        if (wallet > 50000000) {
            nwAdvice += `<div class="advice-item warning"><strong>⚠️ High Wallet Cash ($${Math.round(wallet).toLocaleString('en-US')}):</strong> Carrying over $50M in your wallet makes you a prime mugging target during Ranked Wars. Vault it, buy stocks, or store it in faction/ghost trades!</div>`;
        }

        if (stocks < total * 0.4 && bank >= 2000000000) {
            nwAdvice += `<div class="advice-item"><strong>Top Stock Blocks by ROI Order:</strong> Invest excess cash into Torn's highest-ROI dividend blocks in this order:<br>
            1. <strong>SYM</strong> (Symbiotic) — 1x Drug Pack every 7 days (~highest ROI)<br>
            2. <strong>TCI</strong> (Torn City Investments) — +10% Bank Interest (essential with $2B bank)<br>
            3. <strong>FHG</strong> (Feathery Hotels) — 1x Feathery Hotel Coupon every 7 days<br>
            4. <strong>MCS</strong> (Mc Smoogle) — +100 Energy every 7 days<br>
            5. <strong>HRG / PRN</strong> — Property upgrades & Job points</div>`;
        } else if (stocks >= total * 0.5) {
            nwAdvice += `<div class="advice-item success"><strong>✅ Strong Stock Portfolio:</strong> Over 50% of your networth is working in the Stock Market. Make sure you already hold the top-ROI core: <strong>SYM → TCI → FHG → MCS</strong> before stacking lower-ROI blocks.</div>`;
        }

        if (items > 200000000) {
            nwAdvice += `<div class="advice-item warning"><strong>🧹 Inventory Cleanup Recommended ($${Math.round(items).toLocaleString('en-US')} in items):</strong> You have significant networth sitting idle in your inventory! Sell off unused <strong>plushies/flowers, extra clothing, low-tier weapons/armor, and unneeded consumables</strong> in your Bazaar/Item Market, then move that cash into high-ROI Stock Blocks. <em>(Keep only: Xanax, Meds, Temps, Points, and Energy/Happy boosters).</em></div>`;
        } else {
            nwAdvice += `<div class="advice-item success"><strong>✅ Lean Inventory:</strong> Your inventory value ($${Math.round(items).toLocaleString('en-US')}) is lean and not tying up excessive capital.</div>`;
        }

        container.innerHTML = `
            <div class="grid" style="margin-bottom:10px;">
                <div style="background:#15151a; padding:12px; border-radius:6px; border:1px solid #2c2c36;">
                    <div style="font-size:11px; color:#aaa;">Total Networth</div>
                    <div style="font-size:18px; font-weight:bold; color:var(--accent-orange);">$${Math.round(total).toLocaleString('en-US')}</div>
                </div>
                <div style="background:#15151a; padding:12px; border-radius:6px; border:1px solid #2c2c36;">
                    <div style="font-size:11px; color:#aaa;">Stock Market Value</div>
                    <div style="font-size:18px; font-weight:bold; color:var(--accent-green);">$${Math.round(stocks).toLocaleString('en-US')}</div>
                </div>
                <div style="background:#15151a; padding:12px; border-radius:6px; border:1px solid #2c2c36;">
                    <div style="font-size:11px; color:#aaa;">Inventory & Items Worth</div>
                    <div style="font-size:18px; font-weight:bold; color:var(--accent-blue);">$${Math.round(items).toLocaleString('en-US')}</div>
                </div>
            </div>
            <div class="advice-item">
                <strong>Liquid & Bank Breakdown:</strong> City Bank: <b>$${Math.round(bank).toLocaleString('en-US')}</b> | Points: <b>$${Math.round(points).toLocaleString('en-US')}</b> | Wallet: <b>$${Math.round(wallet).toLocaleString('en-US')}</b>
            </div>
            ${nwAdvice}
        `;
    }

    function auditEducation(d) {
        const perks = (d.education_perks || []).join(' ').toLowerCase();
        const container = document.getElementById('educationAdvice');
        if (!container) return;

        const checks = [
            { name: 'Sports Science (Gym Gains)', pass: perks.includes('gym gains') },
            { name: 'Combat Training (Weapon Exp & Damage)', pass: perks.includes('weapon experience') || perks.includes('melee damage') },
            { name: 'Biology (Blood Bags & Med Cooldown)', pass: perks.includes('withdraw and deliver blood') || perks.includes('medical item effectiveness') },
            { name: 'Self Defense / Martial Arts (Passive Stats)', pass: perks.includes('passive strength') || perks.includes('passive defense') || perks.includes('passive dexterity') }
        ];

        container.innerHTML = checks.map(c => `
            <div class="advice-item ${c.pass ? 'success' : 'warning'}">
                <strong>${c.pass ? '✅' : '⚠️'} ${c.name}:</strong> ${c.pass ? 'Completed!' : 'Missing key war/gym perks — prioritize these courses.'}
            </div>
        `).join('');
    }

    function auditBoosterAndSeRoi(d) {
        const container = document.getElementById('boosterRoiContainer');
        if (!container) return;

        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const str = bs.strength || 0;
        const def = bs.defense || 0;
        const spd = bs.speed || 0;
        const dex = bs.dexterity || 0;
        const highestStat = Math.max(str, def, spd, dex);
        const dominantName = str >= Math.max(def, spd, dex) ? 'Strength' :
                             def >= Math.max(spd, dex) ? 'Defense' :
                             spd >= dex ? 'Speed' : 'Dexterity';

        const seItemMap = {
            'Strength': { name: 'Boxing Gloves', id: 330 },
            'Defense': { name: 'Dumbbells', id: 331 },
            'Speed': { name: 'Skateboard', id: 329 },
            'Dexterity': { name: 'Parachute', id: 106 }
        };
        const seEntry = seItemMap[dominantName] || { name: 'Stat Enhancer', id: 330 };
        const seName = seEntry.name;
        const currentSeGain = highestStat * 0.01;

        // Dynamic Market Pricing from Torn API runtime
        const items = globalItemPrices || {};
        const getItemPrice = (id, fallback) => {
            return (items[id] && items[id].market_value && items[id].market_value > 0) ? items[id].market_value : fallback;
        };

        const seCost = getItemPrice(seEntry.id, 450000000);
        const currentSeCostPerStat = currentSeGain > 0 ? (seCost / currentSeGain).toFixed(2) : 0;
        const targetSeGainAt15B = 1500000000 * 0.01;
        const targetSeCostPerStat = (seCost / targetSeGainAt15B).toFixed(2);

        // Individual Energy Cans mapped to Torn item IDs and real energy values
        // 985: Goose Juice (5e)
        // 986: Damp Valley (10e)
        // 987: Crocozade (15e)
        // 530: Munster (20e)
        // 553: Santa Shooters (20e)
        // 532: Red Cow (25e)
        // 554: Rockstar Rudolph (25e)
        // 533: Taurine Elite (30e)
        // 555: X-MASS (30e)
        const canConfigs = [
            { id: 985, name: 'Can of Goose Juice', baseE: 5, defaultPrice: 260000, note: 'Ultra-budget daily training' },
            { id: 986, name: 'Can of Damp Valley', baseE: 10, defaultPrice: 550000, note: 'Steady entry-level volume' },
            { id: 987, name: 'Can of Crocozade', baseE: 15, defaultPrice: 870000, note: 'Solid mid-tier volume' },
            { id: 530, name: 'Can of Munster', baseE: 20, defaultPrice: 1400000, note: 'Great energy throughput' },
            { id: 553, name: 'Can of Santa Shooters', baseE: 20, defaultPrice: 1390000, note: 'Festive 20e alternative' },
            { id: 532, name: 'Can of Red Cow', baseE: 25, defaultPrice: 1880000, note: '⭐ Best balance of energy & speed' },
            { id: 554, name: 'Can of Rockstar Rudolph', baseE: 25, defaultPrice: 1890000, note: '25e alternative variant' },
            { id: 533, name: 'Can of Taurine Elite', baseE: 30, defaultPrice: 3600000, note: '🔥 Max cooldown density (Books/Rushes)' },
            { id: 555, name: 'Can of X-MASS', baseE: 30, defaultPrice: 3600000, note: '30e holiday variant' }
        ];

        // Compute runtime price, boosted energy (+50% faction perk), and cost per energy point
        const calculatedCans = canConfigs.map(c => {
            const price = getItemPrice(c.id, c.defaultPrice);
            const boostedE = c.baseE * 1.5; // +50% Voracity perk
            const costPerE = Math.round(price / boostedE);
            const dailyE = boostedE * 12; // 24h / 2h per can = 12 cans daily
            return {
                id: c.id,
                name: c.name,
                baseE: c.baseE,
                boostedE: boostedE,
                price: price,
                costPerE: costPerE,
                dailyE: dailyE,
                note: c.note
            };
        });

        // SORT CANS STRICTLY BY BEST VALUE ($/Energy ASCENDING)
        calculatedCans.sort((a, b) => a.costPerE - b.costPerE);

        const fhcBaseE = 150;
        const fhcPrice = getItemPrice(367, 14300000);
        const fhcCostPerE = Math.round(fhcPrice / fhcBaseE);

        let canRowsHtml = calculatedCans.map((c, index) => {
            const rankBadge = index === 0 
                ? '<span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">#1 BEST ROI</span>'
                : (index === 1 ? '<span class="badge" style="background:#2196f3; color:#fff; font-size:10px;">#2 VALUE</span>' : '');

            return `
                <tr style="border-bottom:1px solid #282834; font-size:12px; background:${index === 0 ? 'rgba(76, 175, 80, 0.08)' : 'transparent'};">
                    <td style="padding:8px 6px; font-weight:bold; color:#fff;">
                        ${c.name} ${rankBadge}
                    </td>
                    <td style="padding:8px 6px; text-align:center; color:#bbb;">${c.baseE}e <span style="color:var(--accent-green); font-weight:bold;">(${c.boostedE}e)</span></td>
                    <td style="padding:8px 6px; text-align:right; color:#ccc;">$${(c.price / 1e6).toFixed(2)}M</td>
                    <td style="padding:8px 6px; text-align:right; font-weight:bold; color:${c.costPerE <= 60000 ? 'var(--accent-green)' : (c.costPerE <= 75000 ? 'var(--accent-blue)' : 'var(--accent-orange)')};">
                        $${c.costPerE.toLocaleString('en-US')}/e
                    </td>
                    <td style="padding:8px 6px; text-align:center; color:#ddd;">${c.dailyE}e / day</td>
                    <td style="padding:8px 6px; color:#aaa; font-size:11px;">${c.note}</td>
                </tr>
            `;
        }).join('');

        // Dynamic Happy Jump Costs based on live Torn market
        const happyJumpSoftCap = 400000;
        const happyJumpHardCap = 800000;
        const totalStats = str + def + spd + dex;
        const lowestStat = Math.min(str, def, spd, dex);

        const xanaxPrice = getItemPrice(206, 834000);
        const edvdPrice = getItemPrice(366, 3860000);
        const ecstPrice = getItemPrice(197, 40000);
        const costPerHappyJump = Math.round((xanaxPrice * 4) + (edvdPrice * 5) + ecstPrice + 1100000);
        const chocoJumpCost = 6500000;

        let happyJumpStatusHtml = '';

        if (highestStat < happyJumpHardCap) {
            // Player is in prime Happy Jump territory
            // Calculate how many jumps needed to reach 400k in all stats or 800k
            const remainingTo400k = Math.max(0, (happyJumpSoftCap * 4) - totalStats);
            const remainingTo800k = Math.max(0, (happyJumpHardCap * 4) - totalStats);

            // Average gain per 1000e EDVD jump around 50k-200k stats is ~40k - 80k stats
            const avgGainPerEdvdJump = Math.max(35000, Math.min(90000, Math.round(50000 + (highestStat * 0.08))));
            const jumpsTo400k = Math.ceil(remainingTo400k / avgGainPerEdvdJump);
            const jumpsTo800k = Math.ceil(remainingTo800k / avgGainPerEdvdJump);
            const costTo400k = jumpsTo400k * costPerHappyJump;
            const costPerStatGainedHJ = (costPerHappyJump / avgGainPerEdvdJump).toFixed(2);

            happyJumpStatusHtml = `
                <div class="advice-item success" style="border-left-color:var(--accent-green); background:rgba(76, 175, 80, 0.08); padding:12px; border-radius:6px; margin-bottom:14px;">
                    <div style="display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;">
                        <strong style="color:var(--accent-green); font-size:14px;">🚀 PRIME HAPPY JUMP TERRITORY (Under 800k Soft Cap)</strong>
                        <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">Highest Stat: ${formatShortNumber(highestStat)} / 800K</span>
                    </div>
                    <div style="font-size:12px; margin-top:6px; line-height:1.6; color:#ddd;">
                        Your highest stat is <strong>${formatShortNumber(highestStat)}</strong>. In Torn, gym gains scale exponentially with <strong>Happy</strong> below 400k–800k stats.<br>
                        • <strong>Cost per Happy Jump:</strong> ~$27.0M (4x Xanax + 5x EDVDs + 1x Ecstasy + 150e Refill).<br>
                        • <strong>Expected Yield per Jump:</strong> <strong>+${formatNumber(avgGainPerEdvdJump)} stats</strong>.<br>
                        • <strong>Cost per Stat Point:</strong> <strong>~$${costPerStatGainedHJ} / stat</strong> (⚡ Over 10x cheaper than buying Stat Enhancers!).<br>
                        • <strong>Recommended Path to 400K Milestone:</strong> ~<strong>${jumpsTo400k} Happy Jumps</strong> (~$${(costTo400k / 1e6).toFixed(1)}M total investment).<br>
                        • <strong>Budget Alternative (Choco Jump):</strong> If you cannot afford EDVDs, use <strong>48x Big Box of Chocolate</strong> with 1x Ecstasy (~$6.5M/jump, yields ~15k–25k stats).
                    </div>
                </div>
            `;
        } else {
            happyJumpStatusHtml = `
                <div class="advice-item" style="border-left-color:var(--accent-blue); background:rgba(33, 150, 243, 0.08); padding:12px; border-radius:6px; margin-bottom:14px;">
                    <div style="display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:8px;">
                        <strong style="color:var(--accent-blue); font-size:14px;">✅ HAPPY JUMP PHASE COMPLETED (Past 800k Soft Cap)</strong>
                        <span class="badge" style="background:var(--accent-blue); color:#fff; font-size:10px;">Stats: ${formatShortNumber(highestStat)} &gt; 800K</span>
                    </div>
                    <div style="font-size:12px; margin-top:6px; line-height:1.6; color:#bbb;">
                        Your highest stat is <strong>${formatShortNumber(highestStat)}</strong>. You have graduated past the Happy Jump soft cap (400k–800k).<br>
                        • <strong>Why stop Happy Jumps?</strong> Beyond 800k per stat, raw gym gains from natural training equal and exceed the multiplier provided by Happy.<br>
                        • <strong>Optimal Strategy Now:</strong> Switch 100% to <strong>Steady 3x Xanax + 1x Refill daily (~1,000e/day)</strong> + Energy Cans. Spending money on EDVDs now gives fewer stats per dollar than buying Energy Cans.
                    </div>
                </div>
            `;
        }

        // =========================================================================
        // 2. LIFETIME PROGRESSION $/STAT COMPARISON INDEX (HAPPY JUMP VS CANS VS SE)
        // =========================================================================
        let seDiagnosis = '';
        const seGain = Math.max(1, highestStat * 0.01);
        const seCostPoint = (seCost / seGain).toFixed(2);
        if (highestStat >= 1.5e9) {
            seDiagnosis = `
                <div class="advice-item success">
                    <strong>🎉 1.5B Threshold Achieved for ${dominantName}!</strong><br>
                    Your ${dominantName} is <strong>${formatShortNumber(highestStat)}</strong>. Using a <strong>${seName} (+1%)</strong> now yields <strong>+${formatShortNumber(currentSeGain)} stats</strong> for ~$460M (<strong>~$${currentSeCostPerStat} / stat</strong>).<br>
                    👉 <strong>SEs are officially more cost-efficient than Cans and FHCs for your account.</strong> Start buying ${seName}s for your booster cooldown!
                </div>
            `;
        } else {
            const gap = 1.5e9 - highestStat;
            seDiagnosis = `
                <div class="advice-item warning">
                    <strong>⏳ Hold on Stat Enhancers (SEs) until 1.5B ${dominantName} (Gap: ${formatShortNumber(gap)}):</strong><br>
                    • <strong>Current SE Efficiency:</strong> 1 ${seName} gives <strong>+${formatShortNumber(currentSeGain)} stats</strong> @ ~$460M (<strong>$${currentSeCostPerStat} / stat point</strong>).<br>
                    • <strong>At 1.5B Milestone:</strong> 1 ${seName} will yield <strong>+15,000,000 stats</strong> (<strong>$${targetSeCostPerStat} / stat point</strong>).<br>
                    • <strong>Why Cans/FHCs Win Right Now:</strong> In a Specialist/George's gym with max perks, spending $460M on <strong>20e/25e Cans</strong> gives ~5,000–6,000 Energy, generating <strong>~12M–15M stats</strong>. Buying SEs right now wastes money compared to training energy boosters.
                </div>
            `;
        }

        container.innerHTML = `
            ${happyJumpStatusHtml}

            ${seDiagnosis}

            <!-- Lifetime Training Economy Comparison Matrix -->
            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px; overflow-x:auto;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    💰 Lifetime Torn Training Economy Matrix: Cost Per Stat Point ($/Gain)
                </div>
                <table style="width:100%; border-collapse:collapse; text-align:left; font-size:12px;">
                    <thead>
                        <tr style="border-bottom:2px solid #3e3e4e; color:#9595a5; font-size:11px; text-transform:uppercase;">
                            <th style="padding:6px;">Training Method</th>
                            <th style="padding:6px;">Target Stat Range</th>
                            <th style="padding:6px; text-align:right;">Avg Cost / Method</th>
                            <th style="padding:6px; text-align:right;">Gain per Method</th>
                            <th style="padding:6px; text-align:right;">Cost / Stat Point</th>
                            <th style="padding:6px;">Verdict for Your Account</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr style="border-bottom:1px solid #282834; background:${highestStat < 800000 ? 'rgba(76, 175, 80, 0.1)' : 'transparent'};">
                            <td style="padding:8px 6px; font-weight:bold; color:var(--accent-green);">Erotic DVD Happy Jump</td>
                            <td style="padding:8px 6px;">0 to 400k (Max 800k)</td>
                            <td style="padding:8px 6px; text-align:right;">~$27.0M</td>
                            <td style="padding:8px 6px; text-align:right;">+40,000 to +80,000</td>
                            <td style="padding:8px 6px; text-align:right; font-weight:bold; color:var(--accent-green);">$350 – $650 / stat</td>
                            <td style="padding:8px 6px; font-size:11px;">${highestStat < 800000 ? '<span class="badge" style="background:var(--accent-green); color:#000;">RECOMMENDED NOW</span>' : '<span style="color:#888;">Outgrown (&gt;800k)</span>'}</td>
                        </tr>
                        <tr style="border-bottom:1px solid #282834; background:${highestStat >= 800000 && highestStat < 1.5e9 ? 'rgba(33, 150, 243, 0.1)' : 'transparent'};">
                            <td style="padding:8px 6px; font-weight:bold; color:var(--accent-blue);">3x Xanax + 20e/25e Cans</td>
                            <td style="padding:8px 6px;">800k to 1.50 Billion</td>
                            <td style="padding:8px 6px; text-align:right;">~$2.7M – $3.5M / can</td>
                            <td style="padding:8px 6px; text-align:right;">+600 to +2,500 / can</td>
                            <td style="padding:8px 6px; text-align:right; font-weight:bold; color:var(--accent-blue);">$1,400 – $2,500 / stat</td>
                            <td style="padding:8px 6px; font-size:11px;">${highestStat >= 800000 && highestStat < 1.5e9 ? '<span class="badge" style="background:var(--accent-blue); color:#fff;">RECOMMENDED NOW</span>' : (highestStat < 800000 ? '<span style="color:#888;">Save for post-800k</span>' : '<span style="color:#888;">Surpassed by SEs</span>')}</td>
                        </tr>
                        <tr style="border-bottom:1px solid #282834; background:${highestStat >= 1.5e9 ? 'rgba(255, 153, 0, 0.1)' : 'transparent'};">
                            <td style="padding:8px 6px; font-weight:bold; color:var(--accent-orange);">${seName} (+1% SE)</td>
                            <td style="padding:8px 6px;">1.50 Billion to Infinity</td>
                            <td style="padding:8px 6px; text-align:right;">~$460.0M</td>
                            <td style="padding:8px 6px; text-align:right;">+15M+ stats / use</td>
                            <td style="padding:8px 6px; text-align:right; font-weight:bold; color:var(--accent-orange);">$30.67 / stat</td>
                            <td style="padding:8px 6px; font-size:11px;">${highestStat >= 1.5e9 ? '<span class="badge" style="background:var(--accent-orange); color:#000;">RECOMMENDED NOW</span>' : `<span style="color:var(--accent-red);">Wait (Current: $${currentSeCostPerStat}/stat)</span>`}</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px; overflow-x:auto;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:6px; display:flex; justify-content:space-between;">
                    <span>📊 Energy Cans & FHC Price-to-Energy (ROI) Index</span>
                    <span style="font-size:11px; color:#888;">*Assumes +50% Faction Energy Drink Perk (Voracity / Excursion)</span>
                </div>
                <table style="width:100%; border-collapse:collapse; text-align:left;">
                    <thead>
                        <tr style="border-bottom:2px solid #3e3e4e; color:#9595a5; font-size:11px; text-transform:uppercase;">
                            <th style="padding:6px;">Booster Item</th>
                            <th style="padding:6px; text-align:center;">Base (Boosted)</th>
                            <th style="padding:6px; text-align:right;">Market Price</th>
                            <th style="padding:6px; text-align:right;">Cost / Energy</th>
                            <th style="padding:6px; text-align:center;">24h Cooldown Yield</th>
                            <th style="padding:6px;">Recommendation & Role</th>
                        </tr>
                    </thead>
                    <tbody>
                        ${canRowsHtml}
                        <tr style="border-top:2px solid #3e3e4e; background:rgba(33, 150, 243, 0.08); font-size:12px;">
                            <td style="padding:8px 6px; font-weight:bold; color:var(--accent-blue);">Feathery Hotel Coupon (FHC)</td>
                            <td style="padding:8px 6px; text-align:center; color:#fff;">150e <span style="font-size:10px; color:#aaa;">(Fixed)</span></td>
                            <td style="padding:8px 6px; text-align:right; color:#ccc;">$${(fhcPrice / 1e6).toFixed(2)}M</td>
                            <td style="padding:8px 6px; text-align:right; font-weight:bold; color:var(--accent-orange);">$${fhcCostPerE.toLocaleString('en-US')}/e</td>
                            <td style="padding:8px 6px; text-align:center; color:#ddd;">600e / day <span style="font-size:10px; color:#aaa;">(4 FHCs @ 6h CD)</span></td>
                            <td style="padding:8px 6px; color:#90caf9; font-size:11px;">Fast burst energy, but worse $/e than 5e–25e cans.</td>
                        </tr>
                    </tbody>
                </table>
            </div>

            <div class="grid" style="margin-top:12px; gap:10px;">
                <div style="background:#181820; border:1px solid #2a2a34; padding:12px; border-radius:6px; font-size:12px;">
                    <strong style="color:var(--accent-green);">💡 #1 Best Value Can (${calculatedCans[0].name}):</strong><br>
                    Currently lowest price per energy point at <strong>$${calculatedCans[0].costPerE.toLocaleString('en-US')}/e</strong> ($${(calculatedCans[0].price / 1e6).toFixed(2)}M / can). Top pick for budget training!
                </div>
                <div style="background:#181820; border:1px solid #2a2a34; padding:12px; border-radius:6px; font-size:12px;">
                    <strong style="color:var(--accent-blue);">⚖️ High-Density Sweet Spot (20e–25e):</strong><br>
                    Use <strong>Munster (20e)</strong> or <strong>Red Cow (25e)</strong> for higher daily energy volume without paying the extreme 30e premium.
                </div>
                <div style="background:#181820; border:1px solid #2a2a34; padding:12px; border-radius:6px; font-size:12px;">
                    <strong style="color:var(--accent-orange);">🔥 30e Can Premium (Taurine Elite / X-MASS):</strong><br>
                    Yields up to 540e daily on 24h CD, but costs <strong>$${calculatedCans.find(c => c.baseE === 30)?.costPerE.toLocaleString('en-US') || '80,000'}+ / e</strong>. Best reserved for <strong>Gym Books (+20%)</strong> or SE rush sprints.
                </div>
            </div>
        `;
    }

    // Active War Scanner & Optimal FF / Respect Target Finder (100% Official TDup BSP Server)
    async function scanActiveWarRoster() {
        const key = document.getElementById('apiKeyInput').value.trim();
        const statusEl = document.getElementById('warScanStatus');
        const container = document.getElementById('warRosterContainer');

        if (!key) {
            statusEl.innerHTML = '<span style="color:var(--accent-red);">Please enter your Torn API key above first.</span>';
            return;
        }

        statusEl.innerHTML = '🔍 Detecting active Ranked War from your faction logs...';
        container.innerHTML = '';

        try {
            // 1. Fetch user faction and active ranked wars
            const fUrl = `https://api.torn.com/faction/?selections=basic,rankedwars&key=${key}`;
            const fData = await fetchWithFallback(fUrl);

            if (fData.error) {
                statusEl.innerHTML = `<span style="color:var(--accent-red);">Faction Error: ${fData.error.error}</span>`;
                return;
            }

            const myFactionId = fData.ID;
            const myFactionName = fData.name || 'Your Faction';
            const rankedWars = fData.rankedwars || {};
            const warEntries = Object.entries(rankedWars);

            // Find ongoing or most recent war
            let activeWar = null;
            let activeWarId = null;

            for (const [wId, warObj] of warEntries) {
                const war = warObj.war || {};
                if (war.end === 0 || war.winner === 0) {
                    activeWar = warObj;
                    activeWarId = wId;
                    break;
                }
            }

            if (!activeWar && warEntries.length > 0) {
                const [lastId, lastObj] = warEntries[warEntries.length - 1];
                activeWar = lastObj;
                activeWarId = lastId;
            }

            if (!activeWar) {
                statusEl.innerHTML = `<div class="alert-banner alert-info">ℹ️ <div><strong>No Ranked War Found:</strong> Your faction (<strong>${myFactionName}</strong>) does not have an active or recent Ranked War logged in the API.</div></div>`;
                return;
            }

            const factionsObj = activeWar.factions || {};
            const factionIds = Object.keys(factionsObj);
            const enemyFactionId = factionIds.find(id => id != myFactionId) || factionIds[0];
            const enemyFactionName = factionsObj[enemyFactionId]?.name || `Enemy Faction [${enemyFactionId}]`;

            statusEl.innerHTML = `⚔️ Identified War against <strong>${enemyFactionName}</strong> (ID: ${enemyFactionId}). Fetching enemy roster...`;

            // 2. Fetch Enemy Faction Roster
            const enemyFactionUrl = `https://api.torn.com/faction/${enemyFactionId}?selections=basic&key=${key}`;
            const enemyFactionData = await fetchWithFallback(enemyFactionUrl);

            if (enemyFactionData.error) {
                statusEl.innerHTML = `<span style="color:var(--accent-red);">Could not load enemy roster: ${enemyFactionData.error.error}</span>`;
                return;
            }

            const members = enemyFactionData.members || {};
            const memberList = Object.entries(members).map(([id, m]) => ({
                id,
                name: m.name,
                level: m.level,
                daysInFaction: m.days_in_faction,
                lastAction: m.last_action?.relative || 'Unknown',
                status: m.last_action?.status || 'Offline',
                state: m.status?.state || 'Okay',
                stateDesc: m.status?.description || 'Okay'
            }));

            if (memberList.length === 0) {
                statusEl.innerHTML = `<span style="color:var(--accent-red);">No members found in enemy faction ${enemyFactionName}.</span>`;
                return;
            }

            // Determine player's battle stats for Fair Fight calculations
            let myTotal = 2474700000;
            if (globalPlayerData) {
                const myBs = globalPlayerData.strength !== undefined ? globalPlayerData : (globalPlayerData.battlestats || {});
                const calcTotal = (myBs.strength || 0) + (myBs.defense || 0) + (myBs.speed || 0) + (myBs.dexterity || 0);
                if (calcTotal > 0) myTotal = calcTotal;
            }

            const allMembers = [...memberList].sort((a, b) => b.level - a.level);
            statusEl.innerHTML = `Fetching live official BSP predictions directly from TDup's server for <strong>all ${allMembers.length} targets</strong> in <strong>${enemyFactionName}</strong>...`;

            let analyzedTargets = [];
            const batchSize = 3;

            for (let i = 0; i < allMembers.length; i += batchSize) {
                const batch = allMembers.slice(i, i + batchSize);
                statusEl.innerHTML = `Querying TDup BSP Server: <strong>${Math.min(i + batchSize, allMembers.length)} / ${allMembers.length}</strong> targets in <strong>${enemyFactionName}</strong>...`;

                const batchPromises = batch.map(async (m) => {
                    try {
                        let estStats = 0;

                        // Query TDup lol-manager API directly
                        const bspData = await requestBspFromExtension(key, m.id);
                        let parsedBsp = bspData;
                        if (typeof bspData === 'string') {
                            try { parsedBsp = JSON.parse(bspData); } catch (e) {}
                        }

                        if (parsedBsp && (parsedBsp.TBS || parsedBsp.TBS_Raw || parsedBsp.EstimatedStats)) {
                            estStats = Math.round(parsedBsp.TBS || parsedBsp.TBS_Raw || parsedBsp.EstimatedStats);
                        }

                        if (estStats > 0) {
                            const statRatio = estStats / Math.max(1, myTotal);

                            // Official Fair Fight formula
                            let ff = 1.0;
                            if (statRatio > 0) {
                                ff = 1 + (8 / 3) * Math.sqrt(Math.max(0.01, statRatio));
                                ff = Math.min(3.0, Math.max(1.0, ff));
                            }

                            let tier = '';
                            let badgeColor = '';
                            let sortScore = 0;

                            if (statRatio > 1.25) {
                                tier = '⚠️ High Threat (Surpasses You)';
                                badgeColor = 'background:var(--accent-red); color:#fff;';
                                sortScore = 10;
                            } else if (statRatio >= 0.70 && statRatio <= 1.25) {
                                tier = '🔥 Optimal Respect Farm (2.6x–3.0x FF)';
                                badgeColor = 'background:var(--accent-orange); color:#000; font-weight:bold;';
                                sortScore = 100;
                            } else if (statRatio >= 0.25 && statRatio < 0.70) {
                                tier = '⚡ Safe Farm (2.0x–2.5x FF)';
                                badgeColor = 'background:var(--accent-green); color:#000;';
                                sortScore = 80;
                            } else {
                                tier = 'Easy Chain Target (1.2x–1.9x FF)';
                                badgeColor = 'background:#444; color:#ccc;';
                                sortScore = 50;
                            }

                            return {
                                id: m.id,
                                name: m.name,
                                level: m.level,
                                state: m.state,
                                stateDesc: m.stateDesc,
                                estStats,
                                bspMin: Math.round(estStats * 0.85),
                                bspMax: Math.round(estStats * 1.15),
                                statRatio,
                                ff: ff.toFixed(2),
                                tier,
                                badgeColor,
                                sortScore
                            };
                        }
                    } catch (e) {}
                    return null;
                });

                const batchResults = await Promise.all(batchPromises);
                batchResults.filter(Boolean).forEach(res => analyzedTargets.push(res));
                await new Promise(r => setTimeout(r, 120));
            }

            let bestTargets = analyzedTargets.filter(t => t.statRatio <= 1.25 && parseFloat(t.ff) >= 2.0);
            if (bestTargets.length === 0) {
                bestTargets = analyzedTargets.filter(t => t.statRatio <= 1.30);
            }
            bestTargets.sort((a, b) => parseFloat(b.ff) - parseFloat(a.ff) || b.sortScore - a.sortScore || b.level - a.level);

            statusEl.innerHTML = `
                <div style="color:var(--accent-green); margin-bottom:8px;">
                    ✅ <strong>Live Official BSP Scan:</strong> Showing <strong>${bestTargets.length} Optimal Fair Fight (FF) Respect Targets</strong> in <strong>${enemyFactionName}</strong> directly from TDup's official server:
                </div>
            `;

            let tableRows = bestTargets.map(t => {
                const statusDot = t.state === 'Hospital' 
                    ? '<span style="color:var(--accent-orange);">🏥 Hospital</span>' 
                    : (t.state === 'Okay' ? '<span style="color:var(--accent-green);">🟢 Okay</span>' : `<span style="color:var(--accent-blue);">${t.state}</span>`);

                return `
                    <tr style="border-bottom: 1px solid #282834; font-size:12px;">
                        <td style="padding:10px 8px;">
                            <strong>${t.name}</strong> <span style="font-size:11px; color:#888;">[${t.id}]</span>
                        </td>
                        <td style="padding:10px 8px; text-align:center;">Lvl ${t.level}</td>
                        <td style="padding:10px 8px; text-align:center;">${statusDot}</td>
                        <td style="padding:10px 8px; text-align:right;">
                            <div style="font-weight:bold; color:var(--accent-orange); font-size:13px;">~${formatShortNumber(t.estStats)}</div>
                            <div style="font-size:10px; color:#777;">Range: ${formatShortNumber(t.bspMin)}–${formatShortNumber(t.bspMax)}</div>
                        </td>
                        <td style="padding:10px 8px; text-align:center;">
                            <span class="badge" style="background:#222; border:1px solid #ff9900; color:#ff9900; font-size:10px;">⚡ Live BSP API</span>
                        </td>
                        <td style="padding:10px 8px; text-align:center;">
                            <b style="color:var(--accent-orange); font-size:15px;">${t.ff}x</b>
                        </td>
                        <td style="padding:10px 8px; text-align:center;">
                            <span class="badge" style="${t.badgeColor}; font-size:10px; padding:3px 8px;">${t.tier}</span>
                        </td>
                        <td style="padding:10px 8px; text-align:center;">
                            <button onclick="document.getElementById('enemyIdInput').value='${t.id}'; scoutEnemy(); window.location.hash='#enemyScoutResult';" style="background:#222; border:1px solid #555; color:#fff; padding:4px 10px; border-radius:4px; font-size:11px; cursor:pointer;">
                                Strategy
                            </button>
                        </td>
                    </tr>
                `;
            }).join('');

            container.innerHTML = `
                <table style="width:100%; border-collapse:collapse; margin-top:8px;">
                    <thead>
                        <tr style="border-bottom:2px solid #3e3e4e; color:#9595a5; font-size:11px; text-transform:uppercase;">
                            <th style="padding:8px; text-align:left;">Enemy Target</th>
                            <th style="padding:8px; text-align:center;">Level</th>
                            <th style="padding:8px; text-align:center;">Status</th>
                            <th style="padding:8px; text-align:right;">Official BSP Stats</th>
                            <th style="padding:8px; text-align:center;">Source</th>
                            <th style="padding:8px; text-align:center;">Fair Fight (FF)</th>
                            <th style="padding:8px; text-align:center;">Farming Recommendation</th>
                            <th style="padding:8px; text-align:center;">Combat Plan</th>
                        </tr>
                    </thead>
                    <tbody>
                        ${tableRows}
                    </tbody>
                </table>
            `;

        } catch (e) {
            statusEl.innerHTML = `<span style="color:var(--accent-red);">Failed to scan active war roster: ${e.message || e}</span>`;
        }
    }

    async function scoutEnemy() {
        const key = document.getElementById('apiKeyInput').value.trim();
        const enemyId = document.getElementById('enemyIdInput').value.trim();
        const out = document.getElementById('enemyScoutResult');
        if (!key || !enemyId) {
            out.innerHTML = '<span style="color:var(--accent-red);">Enter both your Torn API key and an Enemy Torn ID.</span>';
            return;
        }

        out.innerHTML = 'Scouting target profile & computing BSP prediction model...';
        try {
            // Trigger direct query to TDup's lol-manager server via Tampermonkey GM_xmlhttpRequest if bridge is present
            if (isBspBridgeActive) {
                window.postMessage({
                    type: 'FETCH_BSP_FOR_TARGET',
                    apiKey: key,
                    targetId: enemyId
                }, '*');
                // Give bridge 400ms to fetch
                await new Promise(r => setTimeout(r, 400));
            }

            const tornUrl = `https://api.torn.com/user/${enemyId}?selections=profile,personalstats&key=${key}`;
            const data = await fetchWithFallback(tornUrl);

            if (data.error) {
                out.innerHTML = `<span style="color:var(--accent-red);">Error: ${data.error.error}</span>`;
                return;
            }

            const pstats = data.personalstats || {};
            const rev = data.revivable === 1 
                ? '<b style="color:var(--accent-green);">YES (Can Revive to attack)</b>' 
                : '<b style="color:var(--accent-red);">NO (Revives Off)</b>';
            const statusColor = data.status?.color === 'green' ? 'var(--accent-green)' : (data.status?.color === 'red' ? 'var(--accent-red)' : 'var(--accent-orange)');
            
            // Intelligence extraction
            const enemyLvl = data.level || 1;
            const enemyHp = data.life?.maximum || (enemyLvl * 50 + 100);
            const xanTaken = pstats.xantaken || 0;
            const refTaken = pstats.refills || 0;
            const eDrinks = pstats.energydrinkused || 0;
            const seUsed = pstats.statenhancersused || 0;
            const defendsWon = pstats.defendswon || 0;
            const defendsLost = pstats.defendslost || 0;
            const defRate = (defendsWon + defendsLost) > 0 ? ((defendsWon / (defendsWon + defendsLost)) * 100).toFixed(1) : 0;
            const attacksStealthed = pstats.attacksstealthed || 0;
            const totalAttacksWon = pstats.attackswon || 1;
            const stealthRatio = (attacksStealthed / totalAttacksWon);
            const ageDays = Math.max(1, data.age || 1);
            const networth = pstats.networth || 0;
            const userActivity = pstats.useractivity || 0;
            const rankTitle = (data.rank || '').toLowerCase();

            // Fetch exact Battle Stats live from TDup's lol-manager server via Tampermonkey extension bridge:
            let bspEstimatedStats = 0;
            let bspSourceTag = 'TDup BSP Extension';
            let bspSubEnd = '';

            try {
                const extData = await requestBspFromExtension(key, enemyId);
                let parsed = extData;
                if (typeof extData === 'string') {
                    try { parsed = JSON.parse(extData); } catch (e) {}
                }

                if (parsed && (parsed.TBS || parsed.TBS_Raw || parsed.EstimatedStats || parsed.Result)) {
                    const rawTbs = parsed.TBS || parsed.TBS_Raw || parsed.EstimatedStats;
                    if (rawTbs && rawTbs > 0) {
                        bspEstimatedStats = Math.round(rawTbs);
                        bspSourceTag = '⚡ Live TDup BSP Extension (Exact)';
                        if (parsed.SubscriptionEnd) {
                            bspSubEnd = `<div style="font-size:10px; color:var(--accent-green); margin-top:2px;">★ Verified BSP Subscription (Active until ${parsed.SubscriptionEnd.split('T')[0]})</div>`;
                        }
                    }
                }
            } catch (bridgeErr) {
                console.error('BSP fetch failed:', bridgeErr);
            }

            if (!bspEstimatedStats || bspEstimatedStats === 0) {
                out.innerHTML = `
                    <div style="background:#15151a; padding:14px; border-radius:6px; border:1px solid #333; margin-top:10px;">
                        <div style="font-size:16px; font-weight:bold; color:#fff; display:flex; justify-content:space-between; align-items:center;">
                            <span>${data.name} [${data.player_id}] — Lvl ${data.level}</span>
                            <span style="font-size:12px; color:#aaa;">Rank: ${data.rank || 'Unknown'}</span>
                        </div>
                        <div style="margin-top:6px; font-size:12px; color:#ccc;">
                            Faction: <b style="color:var(--accent-orange);">${data.faction?.faction_name || 'None'}</b> [${data.faction?.position || 'Member'}] | 
                            Company: <b>${data.job?.company_name || 'None'}</b> [${data.job?.position || 'None'}] | 
                            Last Action: <b>${data.last_action?.relative || 'Unknown'}</b>
                        </div>
                        <div style="margin-top:12px; padding:12px; border-radius:6px; background:rgba(255, 75, 75, 0.1); border:1px solid var(--accent-red); color:var(--accent-red); font-size:13px;">
                            <strong>⚠️ No BSP Battle Stats Available:</strong><br>
                            Local math calculation has been completely removed per configuration.<br>
                            Unable to fetch official stats from TDup's BSP server. Please ensure:
                            <ul style="margin:6px 0 0 16px; padding:0;">
                                <li>Tampermonkey extension is installed and running with <code>bsp_bridge.user.js</code>.</li>
                                <li>In Chrome <code>chrome://extensions/</code>, "Allow access to file URLs" is toggled ON for Tampermonkey.</li>
                                <li>Your BSP subscription is active on lol-manager.com.</li>
                            </ul>
                        </div>
                    </div>
                `;
                return;
            }

            // Dynamic Confidence Range (±15% for veteran models)
            const bspMin = Math.round(bspEstimatedStats * 0.85);
            const bspMax = Math.round(bspEstimatedStats * 1.15);

            const spyHtml = `
                <div style="margin-top:12px; background:#1b1d24; border:1px solid #3c3c4c; border-radius:6px; padding:12px;">
                    <div style="font-weight:bold; color:var(--accent-orange); font-size:13px; display:flex; justify-content:space-between; align-items:center;">
                        <span>🤖 BSP (Battle Stats Predictor) Model</span>
                        <span class="badge" style="background:#ff9900; color:#000; font-size:10px;">${bspSourceTag}</span>
                    </div>
                    <div style="font-size:12px; color:#bbb; margin-top:6px;">
                        Analyzed <strong>${formatNumber(xanTaken)} Xanax</strong>, <strong>${formatNumber(refTaken)} Refills</strong>, <strong>Level ${enemyLvl}</strong>, and <strong>${formatNumber(ageDays)} days</strong> of account progression:
                    </div>
                    <div style="margin:10px 0; background:#141419; padding:10px 14px; border-radius:6px; display:flex; justify-content:space-between; align-items:center;">
                        <div>
                            <div style="font-size:11px; color:#888;">PREDICTED TOTAL BATTLE STATS (TBS)</div>
                            <div style="font-size:20px; font-weight:bold; color:var(--accent-orange);">~${formatShortNumber(bspEstimatedStats)}</div>
                        </div>
                        <div style="text-align:right;">
                            <div style="font-size:11px; color:#888;">CONFIDENCE RANGE</div>
                            <div style="font-size:13px; font-weight:bold; color:#fff;">${formatShortNumber(bspMin)} – ${formatShortNumber(bspMax)}</div>
                        </div>
                    </div>
                    <div style="font-size:11px; color:#aaa;">
                        Career Defend Hold: <strong>${defRate}%</strong> (${defRate > 20 ? 'High Def/Armor Tank' : 'Vulnerable'})
                    </div>
                </div>
            `;

            // Tactical Matchup Strategy
            let strategyItems = [];

            if (globalPlayerData) {
                const myBs = globalPlayerData.strength !== undefined ? globalPlayerData : (globalPlayerData.battlestats || {});
                const myTotal = (myBs.strength || 0) + (myBs.defense || 0) + (myBs.speed || 0) + (myBs.dexterity || 0);
                const statRatio = (myTotal / Math.max(1, bspEstimatedStats)).toFixed(2);

                if (myTotal > bspMax * 1.3) {
                    strategyItems.push(`🟢 <strong>Dominant Stat Advantage:</strong> You out-stat their projected top range (~${formatShortNumber(myTotal)} vs ~${formatShortNumber(bspEstimatedStats)}). Easy 1–2 turn solo kill expected.`);
                } else if (bspMin > myTotal * 1.3) {
                    strategyItems.push(`🔴 <strong>High Risk / Stat Disadvantage:</strong> Target's projected stats (~${formatShortNumber(bspEstimatedStats)}) surpass your total (~${formatShortNumber(myTotal)}). <strong>Do NOT attack solo!</strong> Request group assist or debuff opener.`);
                } else {
                    strategyItems.push(`🟡 <strong>Competitive Matchup (${statRatio}x estimated ratio):</strong> Close match (~${formatShortNumber(myTotal)} vs ~${formatShortNumber(bspEstimatedStats)}). Tactical temporaries and special ammo will decide the outcome.`);
                }

                if (defRate > 22) {
                    strategyItems.push(`<strong>High Defend Rate (${defRate}%):</strong> Wins nearly a quarter of defends! Indicates heavy <strong>Defense</strong> or <strong>Dune/Assault Armor</strong>. <strong>Equip Piercing Ammo</strong> to bypass mitigation.`);
                } else {
                    strategyItems.push(`<strong>Low Defend Rate (${defRate}%):</strong> Low defensive hold rate. Vulnerable to fast burst damage.`);
                }

                if (stealthRatio > 0.35) {
                    strategyItems.push(`<strong>High Stealth Rogue:</strong> ${attacksStealthed} stealthed hits indicates high Speed & Dexterity. <strong>Throw Pepper Spray (-80% Dex) or Flash Grenade</strong> on Turn 1!`);
                }

                if (pstats.piercingammoused > 1000 || pstats.incendiaryammoused > 1000) {
                    strategyItems.push(`<strong>Special Ammo User:</strong> Enemy has fired ${formatNumber(pstats.specialammoused)} special rounds. Wear full 5/5 Armor and throw a <strong>Smoke Grenade (+25% Defense)</strong>.`);
                }

                strategyItems.push(`<strong>Target Max Life:</strong> ~${formatNumber(enemyHp)} HP.`);
            }

            // Status check
            let stateAdvice = '';
            if (data.status?.state === 'Hospital') {
                stateAdvice = `<div style="margin-top:6px; color:var(--accent-orange);">🏥 <strong>Target is in Hospital:</strong> ${data.status.details} ${data.revivable === 1 ? '— You can have a faction reviver revive them for an immediate chain hit!' : '— Revives are OFF. Wait for them to exit.'}</div>`;
            } else if (data.status?.state === 'Okay') {
                stateAdvice = `<div style="margin-top:6px; color:var(--accent-green);">🟢 <strong>Target is ACTIVE & OKAY:</strong> Vulnerable to attack right now.</div>`;
            } else {
                stateAdvice = `<div style="margin-top:6px; color:var(--accent-blue);">✈️ <strong>Target Status:</strong> ${data.status?.description}</div>`;
            }

            // =========================================================================
            // Historical Attack Logs Match: On-Demand Deep Search for Target ID
            // =========================================================================
            const myId = globalPlayerData?.player_id || 0;
            
            // 1. Gather all fights already in globalAttacks / archive
            let targetFights = Object.values(globalAttacks || {}).filter(atk => 
                (atk.attacker_id == enemyId && atk.defender_id == myId) || 
                (atk.attacker_id == myId && atk.defender_id == enemyId)
            );

            // 2. If fewer than 10 fights found in current buffer, query Torn's attacksfull pages backward on demand!
            try {
                let toTimestamp = null;
                // Determine lowest timestamp in current globalAttacks
                const allTimestamps = Object.values(globalAttacks || {}).map(a => a.timestamp_started || a.timestamp_ended).filter(t => t > 0);
                if (allTimestamps.length > 0) {
                    toTimestamp = Math.min(...allTimestamps) - 1;
                }

                // Query up to 3 earlier attack blocks (~3,000 fights backward) specifically looking for this target
                let pageCount = 0;
                while (targetFights.length < 10 && pageCount < 3 && toTimestamp && toTimestamp > 1600000000) {
                    pageCount++;
                    const pageUrl = `https://api.torn.com/user/?selections=attacksfull&to=${toTimestamp}&key=${key}`;
                    const pageData = await fetchWithFallback(pageUrl).catch(() => ({ attacks: {} }));
                    const pageAttacks = pageData.attacks || {};
                    const pageList = Object.values(pageAttacks);
                    if (pageList.length === 0) break;

                    let oldestInPage = toTimestamp;
                    pageList.forEach(atk => {
                        const ts = atk.timestamp_started || atk.timestamp_ended || 0;
                        if (ts > 0 && ts < oldestInPage) oldestInPage = ts;

                        if ((atk.attacker_id == enemyId && atk.defender_id == myId) || 
                            (atk.attacker_id == myId && atk.defender_id == enemyId)) {
                            // Check for duplicates
                            if (!targetFights.some(f => (f.code && f.code === atk.code) || (f.timestamp_started === atk.timestamp_started))) {
                                targetFights.push(atk);
                            }
                        }
                        // Also persist into globalAttacks
                        if (atk.code) globalAttacks[atk.code] = atk;
                    });

                    toTimestamp = oldestInPage - 1;
                }

                // Update persistent localStorage cache with any newly discovered historical fights
                try {
                    localStorage.setItem('twa_combat_history_archive', JSON.stringify(globalAttacks));
                } catch (e) {}
            } catch (deepSearchErr) {
                console.warn('[TornWarAdvisor] Deep attack search error:', deepSearchErr);
            }

            // Sort fights chronologically descending (newest first)
            targetFights.sort((a, b) => (b.timestamp_started || b.timestamp_ended || 0) - (a.timestamp_started || a.timestamp_ended || 0));

            let logHistoryHtml = '';
            let logStrategyBonus = [];

            if (targetFights.length > 0) {
                let winsAgainstThem = 0;
                let lossesAgainstThem = 0;
                let defendedAgainstThem = 0;
                let defendsLostToThem = 0;
                let latestFight = targetFights[0];
                let latestFf = parseFloat(latestFight.modifiers?.fair_fight) || 1.0;

                targetFights.forEach(atk => {
                    const isMeAttacker = (atk.attacker_id == myId);
                    if (isMeAttacker) {
                        if (atk.result === 'Attacked' || atk.result === 'Mugged' || atk.result === 'Hospitalized') winsAgainstThem++;
                        else if (atk.result === 'Lost') lossesAgainstThem++;
                    } else {
                        if (atk.result === 'Lost') defendedAgainstThem++; // They attacked you and lost!
                        else defendsLostToThem++; // They attacked you and won
                    }
                });

                // Derived battle log intelligence
                let historyVerdict = '';
                if (winsAgainstThem > 0 && lossesAgainstThem === 0) {
                    historyVerdict = `🟢 <strong>Proven Matchup Record:</strong> You have beaten ${data.name} in <strong>${winsAgainstThem}/${winsAgainstThem}</strong> past attacks! Highly safe repeat target.`;
                } else if (lossesAgainstThem > 0 && winsAgainstThem === 0) {
                    historyVerdict = `🔴 <strong>Confirmed Loss in Logs:</strong> You lost ${lossesAgainstThem} time(s) to ${data.name}. Do NOT attack without debuffs (Tear Gas/Flash) or group assist!`;
                } else if (winsAgainstThem > 0 && lossesAgainstThem > 0) {
                    historyVerdict = `🟡 <strong>Contested Matchup:</strong> ${winsAgainstThem} win(s), ${lossesAgainstThem} loss(es). Tactical temp items & turn 1 weapon selection are mandatory.`;
                } else if (defendedAgainstThem > 0 && defendsLostToThem === 0) {
                    historyVerdict = `🛡️ <strong>Defend Proven:</strong> You successfully repelled their attacks ${defendedAgainstThem} time(s). Your defensive stats hold against their build!`;
                } else if (defendsLostToThem > 0) {
                    historyVerdict = `⚠️ <strong>Attacked You:</strong> They defeated you in ${defendsLostToThem} past attacks (${defendedAgainstThem} defended). Retaliate with caution or use Piercing/Tracer ammo.`;
                } else {
                    historyVerdict = `⚔️ <strong>${targetFights.length} Historical Fights Found:</strong> Stalemate / inconclusive past encounters. Use proper weapon and temporary item openers.`;
                }

                // Fair Fight derived from actual combat
                if (latestFf >= 2.5) {
                    logStrategyBonus.push(`🔥 <strong>Actual Combat Fair Fight (${latestFf}x):</strong> Historical logs confirm this target yields huge respect (${latestFf}x)! High priority war target.`);
                } else if (latestFf <= 1.3) {
                    logStrategyBonus.push(`ℹ️ <strong>Low Combat Fair Fight (${latestFf}x):</strong> Past combat logs show low respect yield. Statistically, you heavily overpower this target.`);
                }

                // Build detailed list of past fights (up to 10 most recent)
                const fightRows = targetFights.slice(0, 10).map((atk, idx) => {
                    const isMeAttacker = (atk.attacker_id == myId);
                    const dateStr = atk.timestamp_started ? new Date(atk.timestamp_started * 1000).toLocaleDateString() : 'Past';
                    const roleBadge = isMeAttacker 
                        ? '<span class="badge" style="background:#ff9900; color:#000; font-size:9px;">OFFENSE</span>' 
                        : '<span class="badge" style="background:#2196f3; color:#fff; font-size:9px;">DEFEND</span>';
                    
                    let resultBadge = '';
                    if (atk.result === 'Attacked' || atk.result === 'Hospitalized' || atk.result === 'Mugged') {
                        resultBadge = isMeAttacker ? '<b style="color:var(--accent-green);">WON</b>' : '<b style="color:var(--accent-red);">LOST</b>';
                    } else if (atk.result === 'Lost') {
                        resultBadge = isMeAttacker ? '<b style="color:var(--accent-red);">LOST</b>' : '<b style="color:var(--accent-green);">DEFENDED</b>';
                    } else {
                        resultBadge = `<b style="color:#aaa;">${atk.result || 'Stalemate'}</b>`;
                    }

                    const ffVal = atk.modifiers?.fair_fight ? `${atk.modifiers.fair_fight}x FF` : (atk.respect ? `${atk.respect} Resp` : '');

                    return `
                        <div style="display:flex; justify-content:space-between; align-items:center; padding:4px 0; border-bottom:1px solid #222; font-size:11px;">
                            <div>
                                ${roleBadge} <span style="color:#ccc; margin-left:4px;">${dateStr}</span> — ${resultBadge} (${atk.result || 'Fight'})
                            </div>
                            <div style="color:var(--accent-orange); font-weight:bold;">
                                ${ffVal}
                            </div>
                        </div>
                    `;
                }).join('');

                logHistoryHtml = `
                    <div style="margin-top:12px; background:#121216; border:1px solid #383848; border-left:4px solid var(--accent-orange); border-radius:6px; padding:12px;">
                        <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom:6px;">
                            <span style="font-weight:bold; color:var(--accent-orange); font-size:13px;">📜 Past Combat Log History (${targetFights.length} Fights Found)</span>
                            <span class="badge" style="background:#ff9900; color:#000; font-size:10px;">Exact Combat Proof</span>
                        </div>
                        <div style="font-size:12px; color:#ddd; line-height:1.5;">
                            ${historyVerdict}
                        </div>
                        <div style="font-size:11px; color:#9595a5; margin:6px 0 8px 0;">
                            Offensive Record: <strong>${winsAgainstThem}W - ${lossesAgainstThem}L</strong> | Defensive Record: <strong>${defendedAgainstThem} Defended - ${defendsLostToThem} Lost</strong>
                        </div>
                        <div style="margin-top:6px; background:#18181f; padding:8px; border-radius:4px; max-height:180px; overflow-y:auto;">
                            <div style="font-size:10px; text-transform:uppercase; color:#888; margin-bottom:4px; font-weight:bold;">Recent Direct Engagements (Up to 10):</div>
                            ${fightRows}
                        </div>
                    </div>
                `;
            } else {
                logHistoryHtml = `
                    <div style="margin-top:12px; background:#141419; border:1px solid #282834; border-radius:6px; padding:10px; font-size:11px; color:#888;">
                        ℹ️ No direct fights against <strong>${data.name}</strong> found in your attack archives. Tactical suggestions are calculated using their live profile and official BSP predictor.
                    </div>
                `;
            }

            // Combine historical intelligence into strategyItems
            if (logStrategyBonus.length > 0) {
                strategyItems = [...logStrategyBonus, ...strategyItems];
            }

            out.innerHTML = `
                <div style="background:#15151a; padding:14px; border-radius:6px; border:1px solid #333; margin-top:10px;">
                    <div style="font-size:16px; font-weight:bold; color:#fff; display:flex; justify-content:space-between; align-items:center;">
                        <span>${data.name} [${data.player_id}] — Lvl ${data.level}</span>
                        <span style="font-size:12px; color:#aaa;">Rank: ${data.rank || 'Unknown'}</span>
                    </div>
                    <div style="margin-top:6px; font-size:12px; color:#ccc;">
                        Faction: <b style="color:var(--accent-orange);">${data.faction?.faction_name || 'None'}</b> [${data.faction?.position || 'Member'}] | 
                        Company: <b>${data.job?.company_name || 'None'}</b> [${data.job?.position || 'None'}] | 
                        Last Action: <b>${data.last_action?.relative || 'Unknown'}</b>
                    </div>
                    <div style="margin-top:6px; font-size:12px;">
                        Status: <b style="color:${statusColor};">${data.status?.description || 'Unknown'}</b> | Revivable: ${rev}
                    </div>
                    ${stateAdvice}

                    ${spyHtml}

                    ${logHistoryHtml}

                    <div style="margin-top:12px; border-top:1px solid #2a2a35; padding-top:10px;">
                        <div style="font-weight:bold; color:var(--accent-orange); font-size:13px; margin-bottom:8px;">⚔️ Tactical Combat Strategy to Defeat ${data.name}:</div>
                        ${strategyItems.map(item => `<div class="advice-item" style="margin-bottom:8px;">${item}</div>`).join('')}
                    </div>
                </div>
            `;
        } catch (e) {
            out.innerHTML = `<span style="color:var(--accent-red);">Failed to scout enemy target: ${e.message || e}</span>`;
        }
    }

    function copyDiscordSummary() {
        if (!globalPlayerData) return;
        const d = globalPlayerData;
        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const total = (bs.strength || 0) + (bs.defense || 0) + (bs.speed || 0) + (bs.dexterity || 0);
        const rev = d.revivable === 1 ? 'OPEN (Needs Fixing!)' : 'OFF (Secured)';
        const text = `**⚔️ Torn War Card: ${d.name} [${d.player_id}] (Lvl ${d.level})**\n` +
                     `• **Total Stats:** ${formatShortNumber(total)} (Str: ${formatShortNumber(bs.strength)} | Def: ${formatShortNumber(bs.defense)} | Spd: ${formatShortNumber(bs.speed)} | Dex: ${formatShortNumber(bs.dexterity)})\n` +
                     `• **Revivable:** ${rev}\n` +
                     `• **Equipped Armor:** ${globalEquipment.filter(e => e.type === 'Armor').length}/5 slots`;
        navigator.clipboard.writeText(text).then(() => {
            alert('War Card copied! Paste it straight into Discord.');
        });
    }

    function auditVeteranPrep(d, attacksObj, pastMonthXan, selectedGoal) {
        const container = document.getElementById('veteranPrepAdvice');
        const pstats = d.personalstats || {};
        const revivable = d.revivable;
        const currentXan = pstats.xantaken || 0;
        const age = Math.max(1, d.age || 1);
        const lifetimePace = (currentXan / age).toFixed(2);
        const company = d.job || {};
        const compType = company.company_type;
        const compName = company.company_name || 'Unemployed';
        const goal = selectedGoal || (document.getElementById('goalSelector')?.value || 'baldr_dex');

        let html = '';

        // 1. Revive Setting Check
        if (revivable === 1) {
            html += `
                <div class="advice-item warning">
                    <strong>⚠️ Revive Setting is OPEN (revivable: 1):</strong> Veteran Ranked War guides mandate setting Revives to <strong>"Nobody"</strong> in Preferences before war starts! Leaving revives open allows enemy revivers to pull you out of the hospital and farm you for free respect.
                </div>
            `;
        } else {
            html += `
                <div class="advice-item success">
                    <strong>✅ Revive Setting Secured:</strong> Your revives are turned off, preventing enemies from revive-farming you during wars.
                </div>
            `;
        }

        // 2. Recent Month Xanax Pace Calculation
        let xanHtml = '';
        if (pastMonthXan !== null && pastMonthXan !== undefined && pastMonthXan > 0) {
            const xan30Days = Math.max(0, currentXan - pastMonthXan);
            const recentPace = (xan30Days / 30).toFixed(2);
            const isGoodPace = recentPace >= 2.0;

            xanHtml = `
                <div class="advice-item ${isGoodPace ? 'success' : 'warning'}">
                    <strong>Recent 30-Day Xanax Pace:</strong> <strong>${xan30Days} Xanax taken in the last 30 days (~${recentPace}/day)</strong>.<br>
                    <span style="font-size:11px; color:#aaa;">(Lifetime Career: ${formatNumber(currentXan)} Xanax over ${age} days = ~${lifetimePace}/day)</span><br>
                    ${isGoodPace 
                        ? '🔥 <strong>Elite Veteran Training Pace!</strong> You are actively maintaining 2–3 Xanax/day. Keep stacking to <strong>1,000E</strong> right before wars.' 
                        : '⚠️ Your recent training pace is below the veteran benchmark of <strong>2.0+ Xanax/day</strong>. Try to consistently pop 3x Xanax/day to accelerate stat gains.'}
                </div>
            `;
        } else {
            xanHtml = `
                <div class="advice-item ${lifetimePace >= 2.0 ? 'success' : ''}">
                    <strong>Xanax Training Pace:</strong> ${formatNumber(currentXan)} total Xanax taken (~<strong>${lifetimePace}/day</strong> career average). Veteran benchmark is <strong>2–3 Xanax/day</strong> + stacking to <strong>1,000E</strong> before war starts.
                </div>
            `;
        }
        html += xanHtml;

        // 3. War Hit Type Check (Hosp vs Leave vs Mug)
        const attackList = Object.values(attacksObj || {});
        let warMugs = 0;
        attackList.forEach(a => {
            if (a.attacker_id == d.player_id && a.ranked_war === 1 && a.result === 'Mugged') {
                warMugs++;
            }
        });

        if (warMugs > 0) {
            html += `
                <div class="advice-item warning">
                    <strong>War Hit Discipline:</strong> You had <strong>${warMugs} Mug(s)</strong> in recent Ranked War hits. Unless ordered by leadership, avoid mugging in RW—it reduces respect earned. Use <strong>Hospitalize</strong> on active threats and <strong>Leave</strong> on offline/easy targets.
                </div>
            `;
        } else {
            html += `
                <div class="advice-item success">
                    <strong>War Hit Discipline:</strong> Clean war hits (no wasted RW mugs). Keep using <strong>Hosp</strong> for active threats and <strong>Leave</strong> for quick-farming.
                </div>
            `;
        }

        // 4. Veteran Hospital & Medical Protocol Card
        html += `
            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    🏥 Veteran Medical & Hospitalization Protocol (RW Standard)
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Mandatory)</span> 
                        <strong>Blood Bags (20x in Bag):</strong> Carry 10–20 compatible Blood Bags (O-, A+, etc.) for instant 20–30% life replenishment after winning tough fights.
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Efficiency)</span> 
                        <strong>Small First Aid Kits (SFAKs):</strong> Use SFAKs to trim down small remaining hospital timers (10–25m) without wasting high medical cooldown hours.
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-blue); color:#fff; font-size:10px;">B-TIER (Long Stacks)</span> 
                        <strong>Morphine:</strong> Only pop Morphine when your hospital timer exceeds 3+ hours to clear bulk time in one shot.
                    </div>
                    <div>
                        <span class="badge" style="background:#555; color:#fff; font-size:10px;">CRITICAL RULE</span> 
                        <span style="color:#ff8a80;">Never take First Aid Kits if medical cooldown is near 6 hours unless an immediate war push is called by faction leaders.</span>
                    </div>
                </div>
            </div>
        `;

        // 5. Dynamic Veteran Company Specialization Hierarchy based on Selected Build
        let compFocusLabel = '';
        let compSTier = '';
        let compATier = '';
        let compBTier = '';

        if (goal.includes('dex')) {
            compFocusLabel = 'Dexterity Evasion Focus';
            compSTier = `<strong>10* Gents Strip Club:</strong> +25% passive Dexterity, +10% Dex gym gains, 50e daily energy special. (The absolute undisputed king for Dex rogues).`;
            compATier = `<strong>10* Fitness Center:</strong> +3% all gym gains, +20e train special • <strong>10* Sweet Shop / Candle Shop:</strong> Steady energy/cans for daily volume.`;
            compBTier = `<strong>10* Mining Company:</strong> +30% passive Defense (helps patch up Dex rogue defense baseline).`;
        } else if (goal.includes('def')) {
            compFocusLabel = 'Defense Tank Focus';
            compSTier = `<strong>10* Mining Company:</strong> +30% passive Defense, +10% Def gym gains, 50e daily energy special. (The undisputed meta company for Defense tanks).`;
            compATier = `<strong>10* Fitness Center:</strong> +3% all gym gains, +20e train special • <strong>10* Oil Rig:</strong> +30% Strength (synergizes with secondary tank brawling).`;
            compBTier = `<strong>10* Sweet Shop / Candle Shop:</strong> High energy conversion for rapid training volume.`;
        } else if (goal.includes('str')) {
            compFocusLabel = 'Strength Brawler Focus';
            compSTier = `<strong>10* Oil Rig:</strong> +30% passive Strength, +10% Str gym gains, 50e daily energy special. (Peak damage multiplier for Strength one-punch builds).`;
            compATier = `<strong>10* Fitness Center:</strong> +3% all gym gains, +20e train special • <strong>10* Mining Company:</strong> +30% Defense to survive counter-strikes.`;
            compBTier = `<strong>10* Sweet Shop / Candle Shop:</strong> Continuous energy flow for daily heavy gym sets.`;
        } else if (goal.includes('spd')) {
            compFocusLabel = 'Speed Assassin Focus';
            compSTier = `<strong>10* Amusement Park:</strong> +10 Free Energy & +2 Nerve per day, +25% Speed gym gains, or <strong>10* Fitness Center:</strong> +3% all gym gains and +20e train special.`;
            compATier = `<strong>10* Gents Strip Club / Ladies Strip Club:</strong> High daily energy specials (50e) to accelerate Speed training in Total Rebound.`;
            compBTier = `<strong>10* Candle Shop / Sweet Shop:</strong> Steady energy/can supply for rapid gym unlocks.`;
        } else {
            // Pure balanced or auto-balanced
            compFocusLabel = 'Balanced Titan Focus (All-Around Multipliers)';
            compSTier = `<strong>10* Fitness Center:</strong> +3% all gym gains, +20e train special (perfect for 1:1:1:1 training across all 4 stats), or <strong>10* Sweet Shop / Candle Shop</strong> for maximum daily energy volume.`;
            compATier = `<strong>10* Mining Company (+30% Def)</strong> or <strong>10* Gents Strip Club (+25% Dex):</strong> Massive passive stat multipliers to boost overall tankiness in wars.`;
            compBTier = `<strong>10* Oil Rig:</strong> +30% passive Strength for extra punch.`;
        }

        html += `
            <div style="margin-top:12px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px; display:flex; justify-content:space-between; flex-wrap:wrap; gap:6px;">
                    <span>🏢 Company Perk Hierarchy for Your Build (Current: ${compName})</span>
                    <span style="font-size:11px; color:#aaa;">${compFocusLabel}</span>
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (${compFocusLabel})</span> 
                        ${compSTier}
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (High Synergy)</span> 
                        ${compATier}
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-blue); color:#fff; font-size:10px;">B-TIER (Support)</span> 
                        ${compBTier}
                    </div>
                    <div>
                        <span class="badge" style="background:#555; color:#fff; font-size:10px;">F-TIER (Dead End)</span> 
                        <span style="color:#aaa;">Grocery Store, Flower Shop, Adult Novelties without 10* (Low perk utility for combat stat scaling).</span>
                    </div>
                </div>
            </div>
        `;

        container.innerHTML = html;
    }

    // 1. Special Ammo Audit
    function renderEquipmentAndAmmo(equipment) {
        const gearGrid = document.getElementById('equippedGearGrid');
        const ammoAlert = document.getElementById('ammoAlertContainer');

        if (equipment.length === 0) {
            gearGrid.innerHTML = '<div style="color:#888; font-size:12px;">Could not load equipment (API v2 requires Minimal+ key).</div>';
            ammoAlert.innerHTML = '';
            return;
        }

        let ammoWarnings = [];
        equipment.forEach(item => {
            if (item.type === 'Weapon' && item.sub_type !== 'Temporary' && item.ammo) {
                if (item.ammo.type === 'Standard') {
                    ammoWarnings.push(`Your <strong>${item.name}</strong> is loaded with <strong>Standard Ammo</strong>! Switch to <strong>Hollow Point</strong> (damage), <strong>Piercing</strong> (armor penetration), or <strong>Tracer</strong> (accuracy) for Ranked Wars.`);
                }
            }
        });

        if (ammoWarnings.length > 0) {
            ammoAlert.innerHTML = `
                <div class="alert-banner alert-warning">
                    ⚠️ <div>${ammoWarnings.join('<br>')}</div>
                </div>
            `;
        } else {
            ammoAlert.innerHTML = `
                <div class="alert-banner alert-info" style="color:var(--accent-green); border-color:var(--accent-green); background:rgba(76,175,80,0.1);">
                    ✅ <div><strong>Special Ammo Loaded:</strong> Your equipped weapons have special combat ammunition equipped.</div>
                </div>
            `;
        }

        gearGrid.innerHTML = equipment.map(item => {
            const rarityClass = item.rarity ? `rarity-${item.rarity.toLowerCase()}` : '';
            let statsText = '';
            if (item.stats) {
                if (item.stats.damage) statsText += `DMG: ${item.stats.damage} | ACC: ${item.stats.accuracy} `;
                if (item.stats.armor) statsText += `Armor: ${item.stats.armor} `;
                if (item.stats.quality) statsText += `| Q: ${item.stats.quality}%`;
            }
            const bonuses = (item.bonuses || []).map(b => `${b.title} (${b.value}%)`).join(', ');
            const ammo = item.ammo ? `Ammo: <b>${item.ammo.type}</b> (${item.ammo.name})` : '';
            const mods = (item.mods && item.mods.length) ? `Mods: ${item.mods.map(m => m.name).join(', ')}` : '';

            return `
                <div class="gear-item ${rarityClass}">
                    <div class="gear-name">${item.name} <span style="font-size:10px; color:#888; font-weight:normal;">(${item.sub_type || item.type})</span></div>
                    <div class="gear-meta">${statsText}</div>
                    ${ammo || mods ? `<div class="gear-meta">${[ammo, mods].filter(Boolean).join(' • ')}</div>` : ''}
                    ${bonuses ? `<div class="gear-bonus">★ ${bonuses}</div>` : ''}
                </div>
            `;
        }).join('');

        // Special Ammo Tactical Matchup Matrix
        gearGrid.innerHTML += `
            <div style="grid-column: 1 / -1; margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    🎯 Veteran Special Ammo Matchup Strategy (When to Load What)
                </div>
                <div style="display:grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap:10px; font-size:12px;">
                    <div style="background:#191922; padding:10px; border-radius:6px; border-left:3px solid var(--accent-orange);">
                        <strong style="color:var(--accent-orange);">Piercing Ammo</strong><br>
                        <span style="color:#aaa;">Target:</span> High-Defense Tanks & full Assault/Dune sets.<br>
                        <span style="color:#eee;">Effect:</span> Bypasses heavy armor mitigation. Prevents dealing single-digit damage.
                    </div>
                    <div style="background:#191922; padding:10px; border-radius:6px; border-left:3px solid var(--accent-red);">
                        <strong style="color:var(--accent-red);">Hollow Point Ammo</strong><br>
                        <span style="color:#aaa;">Target:</span> Low-defense targets / Fast burst farm.<br>
                        <span style="color:#eee;">Effect:</span> Massive +Damage bonus to quickly 1-shot enemies.
                    </div>
                    <div style="background:#191922; padding:10px; border-radius:6px; border-left:3px solid var(--accent-blue);">
                        <strong style="color:var(--accent-blue);">Tracer Ammo</strong><br>
                        <span style="color:#aaa;">Target:</span> High Dexterity / Evasion Rogues.<br>
                        <span style="color:#eee;">Effect:</span> Grants high hit accuracy to pierce through dodges.
                    </div>
                    <div style="background:#191922; padding:10px; border-radius:6px; border-left:3px solid #777;">
                        <strong style="color:#888;">Standard Ammo</strong><br>
                        <span style="color:#f44336; font-weight:bold;">Ranked War Warning:</span> Zero damage or accuracy bonuses. Never take Standard ammo into an active war!
                    </div>
                </div>
            </div>
        `;
    }

    // 2. Recent Attacks Audit (Chaining & Farm Detection & Fair Fight Analysis)
    function auditRecentAttacks(attacksObj, myPlayerId) {
        const attackList = Object.values(attacksObj || {});
        const recentWarStatusEl = document.getElementById('recentWarStatus');
        const recentWarRateEl = document.getElementById('recentWarRateDisplay');
        const adviceContainer = document.getElementById('recentAttacksAdvice');

        if (attackList.length === 0) {
            recentWarRateEl.innerText = 'No Data';
            recentWarStatusEl.innerText = 'No recent attacks';
            adviceContainer.innerHTML = '<div class="advice-item">No recent attack log data returned from API.</div>';
            return;
        }

        let myAttacksWon = 0;
        let myAttacksLost = 0;
        let incomingDefendsLost = 0;
        let incomingDefendsWon = 0;
        let attackerCounts = {};
        let offensiveFfSum = 0;
        let offensiveFfCount = 0;
        let defensiveFfSum = 0;
        let defensiveFfCount = 0;

        attackList.forEach(atk => {
            const isAttacker = (atk.attacker_id == myPlayerId);
            const isDefender = (atk.defender_id == myPlayerId);
            const ff = parseFloat(atk.modifiers?.fair_fight) || 1.0;

            if (isAttacker) {
                if (atk.result === 'Attacked' || atk.result === 'Mugged' || atk.result === 'Hospitalized') {
                    myAttacksWon++;
                    offensiveFfSum += ff;
                    offensiveFfCount++;
                } else if (atk.result === 'Lost') {
                    myAttacksLost++;
                    offensiveFfSum += ff;
                    offensiveFfCount++;
                }
            } else if (isDefender) {
                defensiveFfSum += ff;
                defensiveFfCount++;
                if (atk.result === 'Lost') incomingDefendsWon++; // Defender won
                else {
                    incomingDefendsLost++;
                    attackerCounts[atk.attacker_name] = (attackerCounts[atk.attacker_name] || 0) + 1;
                }
            }
        });

        const totalOffensive = myAttacksWon + myAttacksLost;
        const offensiveWinRate = totalOffensive > 0 ? Math.round((myAttacksWon / totalOffensive) * 100) : 100;
        const avgOffensiveFf = offensiveFfCount > 0 ? (offensiveFfSum / offensiveFfCount).toFixed(2) : '1.00';
        const avgDefensiveFf = defensiveFfCount > 0 ? (defensiveFfSum / defensiveFfCount).toFixed(2) : '1.00';

        recentWarRateEl.innerText = `${offensiveWinRate}% Win`;
        recentWarStatusEl.innerText = `${myAttacksWon}W - ${myAttacksLost}L (Avg FF: ${avgOffensiveFf}x)`;

        // Check for chaining/farming
        let farmAlerts = [];
        Object.entries(attackerCounts).forEach(([enemy, count]) => {
            if (count >= 3) {
                farmAlerts.push(`<strong>${enemy}</strong> attacked and defeated you <strong>${count} times</strong> in recent logs!`);
            }
        });

        // Fair fight evaluation
        let ffAdviceHtml = '';
        const numFf = parseFloat(avgOffensiveFf);
        if (numFf >= 2.5) {
            ffAdviceHtml = `
                <div class="advice-item success">
                    <strong>🔥 Elite Fair Fight Average (${avgOffensiveFf}x):</strong> You are picking high-yield targets! In Torn, <strong>FF ranges from 1.0x to 3.0x</strong>. An average above <strong>2.5x</strong> means you fight targets with ~70%+ of your stats, extracting near-maximum respect for your faction without bullying low-stat players.
                </div>
            `;
        } else if (numFf >= 1.8) {
            ffAdviceHtml = `
                <div class="advice-item">
                    <strong>Solid Fair Fight Average (${avgOffensiveFf}x):</strong> Your offensive target selection yields good respect (1.8x – 2.4x). To maximize faction war score, try pushing closer to <strong>2.7x – 3.0x</strong> on opponents you can safely defeat.
                </div>
            `;
        } else {
            ffAdviceHtml = `
                <div class="advice-item warning">
                    <strong>Low Fair Fight Average (${avgOffensiveFf}x):</strong> You are attacking opponents with much lower stats than you (low FF = minimal respect gained, down to 1.0x floor). Target opponents closer to your stat tier to double or triple your respect output!
                </div>
            `;
        }

        let html = `
            <div class="advice-item">
                <strong>Offensive Performance (Recent 100):</strong> Won <strong>${myAttacksWon}</strong> out of ${totalOffensive} attacks (${offensiveWinRate}%). Average Fair Fight Multiplier: <b style="color:var(--accent-orange); font-size:14px;">${avgOffensiveFf}x</b>.
            </div>
            ${ffAdviceHtml}
            <div class="advice-item">
                <strong>Defensive Pressure & Incoming FF:</strong> Defended <strong>${incomingDefendsWon}</strong> times, Defeated <strong>${incomingDefendsLost}</strong> times. Average incoming enemy FF: <b>${avgDefensiveFf}x</b>.
                <div style="font-size:11px; color:#aaa; margin-top:3px;">(If incoming FF is 2.8x–3.0x, enemies with higher or equal stats are targeting you. If it's under 1.5x, weaker players are beating you via group attacks or weapon debuffs).</div>
            </div>
        `;

        if (farmAlerts.length > 0) {
            html += `
                <div class="advice-item warning">
                    <strong>⚠️ Chaining & Farm Alert Detected:</strong><br>
                    ${farmAlerts.join('<br>')}
                    <div style="margin-top:5px; color:#aaa; font-size:11px;">Enemies chain players with predictable stats or low defense during Ranked Wars. Shoring up your lowest defensive stat will stop these repeat attacks.</div>
                </div>
            `;
        } else {
            html += `
                <div class="advice-item success">
                    <strong>Clean War Record:</strong> No single opponent repeatedly farmed you in the recent logs.
                </div>
            `;
        }

        adviceContainer.innerHTML = html;
    }

    // 3. Merits & Combat Perks Audit (Full Veteran S-Tier & Weapon Matcher)
    function auditMeritsAndPerks(d, equipment, selectedGoal) {
        const meritPerks = (d.merit_perks || []).map(p => p.toLowerCase());
        const container = document.getElementById('meritsAdvice');
        if (!container) return;

        const goal = selectedGoal || (document.getElementById('goalSelector')?.value || 'baldr_dex');

        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const str = bs.strength || 0;
        const def = bs.defense || 0;
        const spd = bs.speed || 0;
        const dex = bs.dexterity || 0;
        const nw = d.networth || {};
        const bank = nw.bank || 0;

        // Cumulative merits cost table for 10 ranks:
        // Rank 1: 1, 2: 2, 3: 3, 4: 4, 5: 5, 6: 6, 7: 7, 8: 8, 9: 9, 10: 10 => Total 55 merits for 10/10
        const meritsForRank = (r) => (r * (r + 1)) / 2;

        // 1. Audit Core S-Tier Trees
        let lifeRank = 0;
        let critRank = 0;
        let bankRank = 0;
        let eduRank = 0;

        // 2. Audit Passive Stats
        let passiveStr = 0;
        let passiveDef = 0;
        let passiveSpd = 0;
        let passiveDex = 0;

        // 3. Audit Weapon Masteries
        let rifleMastery = 0;
        let smgMastery = 0;
        let shotgunMastery = 0;
        let pistolMastery = 0;
        let heavyMastery = 0;
        let machineGunMastery = 0;
        let clubbedMastery = 0;
        let slashingMastery = 0;
        let piercingMastery = 0;
        let mechanicalMastery = 0;
        let tempMastery = 0;

        // 4. Audit Wasteful / Non-Combat Trees
        let awarenessRank = 0;
        let mugRank = 0;
        let crimeRank = 0;
        let brawnRank = 0;
        let stealthRank = 0;

        (d.merit_perks || []).forEach(rawPerk => {
            const p = rawPerk.toLowerCase();
            const valMatch = p.match(/(\d+)/);
            const val = valMatch ? parseInt(valMatch[0], 10) : 0;

            if (p.includes('life')) lifeRank = Math.min(10, Math.round(val / 5)); // +5% per rank up to 50%
            if (p.includes('critical hit')) critRank = Math.min(10, Math.round(val / 1)); // +1% per rank up to 10%
            if (p.includes('bank interest')) bankRank = Math.min(10, Math.round(val / 5)); // +5% per rank
            if (p.includes('education length') || p.includes('education time')) eduRank = Math.min(10, Math.round(val / 2));

            // Stat Passives (+3% per rank)
            if (p.includes('passive strength') || (p.includes('strength') && p.includes('passive'))) passiveStr = Math.min(10, Math.round(val / 3));
            if (p.includes('passive defense') || (p.includes('defense') && p.includes('passive'))) passiveDef = Math.min(10, Math.round(val / 3));
            if (p.includes('passive speed') || (p.includes('speed') && p.includes('passive'))) passiveSpd = Math.min(10, Math.round(val / 3));
            if (p.includes('passive dexterity') || (p.includes('dexterity') && p.includes('passive'))) passiveDex = Math.min(10, Math.round(val / 3));

            // Weapon Masteries
            if (p.includes('rifle mastery')) rifleMastery = Math.min(10, val);
            if (p.includes('smg mastery')) smgMastery = Math.min(10, val);
            if (p.includes('shotgun mastery')) shotgunMastery = Math.min(10, val);
            if (p.includes('pistol mastery')) pistolMastery = Math.min(10, val);
            if (p.includes('heavy artillery')) heavyMastery = Math.min(10, val);
            if (p.includes('machine gun')) machineGunMastery = Math.min(10, val);
            if (p.includes('clubbed mastery')) clubbedMastery = Math.min(10, val);
            if (p.includes('slashing mastery')) slashingMastery = Math.min(10, val);
            if (p.includes('piercing mastery')) piercingMastery = Math.min(10, val);
            if (p.includes('mechanical mastery')) mechanicalMastery = Math.min(10, val);
            if (p.includes('temporary weapon')) tempMastery = Math.min(10, val);

            // Wasteful / Situational
            if (p.includes('awareness')) awarenessRank = Math.min(10, Math.round(val / 10)); // +10% per rank
            if (p.includes('mug amount') || p.includes('mugging')) mugRank = Math.min(10, Math.round(val / 5));
            if (p.includes('crime exp')) crimeRank = Math.min(10, Math.round(val / 2));
            if (p.includes('brawn')) brawnRank = Math.min(10, Math.round(val / 3));
            if (p.includes('stealth')) stealthRank = Math.min(10, Math.round(val / 2));
        });

        // Calculate refundable merits from wasteful trees
        let wastedMeritsTotal = 0;
        let wastedItemsList = [];
        if (awarenessRank > 0) {
            const m = meritsForRank(awarenessRank);
            wastedMeritsTotal += m;
            wastedItemsList.push(`Awareness (${awarenessRank}/10 = ${m} merits)`);
        }
        if (mugRank > 0) {
            const m = meritsForRank(mugRank);
            wastedMeritsTotal += m;
            wastedItemsList.push(`Mug Amount (${mugRank}/10 = ${m} merits)`);
        }
        if (stealthRank > 0) {
            const m = meritsForRank(stealthRank);
            wastedMeritsTotal += m;
            wastedItemsList.push(`Stealth (${stealthRank}/10 = ${m} merits)`);
        }

        // Weapon Matching Audit against equipped loadout
        const equippedWeapons = (equipment || []).filter(e => e.type === 'Weapon' && e.sub_type !== 'Temporary');
        let weaponAuditHtml = '';

        if (equippedWeapons.length > 0) {
            let weaponMatches = [];
            let weaponWarnings = [];

            equippedWeapons.forEach(w => {
                const wSub = (w.sub_type || '').toLowerCase();
                const wName = w.name;
                let activeRank = 0;
                let masteryName = '';

                if (wSub.includes('rifle') || wName.includes('Armalite') || wName.includes('Enfield')) {
                    masteryName = 'Rifle Mastery';
                    activeRank = rifleMastery;
                } else if (wSub.includes('smg')) {
                    masteryName = 'SMG Mastery';
                    activeRank = smgMastery;
                } else if (wSub.includes('shotgun')) {
                    masteryName = 'Shotgun Mastery';
                    activeRank = shotgunMastery;
                } else if (wSub.includes('pistol')) {
                    masteryName = 'Pistol Mastery';
                    activeRank = pistolMastery;
                } else if (wSub.includes('heavy') || wSub.includes('artillery')) {
                    masteryName = 'Heavy Artillery';
                    activeRank = heavyMastery;
                } else if (wSub.includes('machine gun')) {
                    masteryName = 'Machine Gun Mastery';
                    activeRank = machineGunMastery;
                } else if (wSub.includes('club') || wName.includes('Baseball') || wName.includes('Hammer')) {
                    masteryName = 'Clubbed Mastery';
                    activeRank = clubbedMastery;
                } else if (wSub.includes('slash') || wName.includes('Macana') || wName.includes('Katana') || wName.includes('Kama')) {
                    masteryName = 'Slashing Mastery';
                    activeRank = slashingMastery;
                } else if (wSub.includes('pierce') || wName.includes('Dagger') || wName.includes('Spear')) {
                    masteryName = 'Piercing Mastery';
                    activeRank = piercingMastery;
                }

                if (masteryName) {
                    if (activeRank >= 10) {
                        weaponMatches.push(`<strong>${wName} (${masteryName}):</strong> Fully backed by <strong>10/10 (+10% DMG, +10% ACC)</strong>!`);
                    } else if (activeRank > 0) {
                        weaponWarnings.push(`<strong>${wName} (${masteryName}):</strong> Currently only at <strong>${activeRank}/10</strong> (+${activeRank}% DMG/ACC). Push to 10/10 for max Ranked War lethality.`);
                    } else {
                        weaponWarnings.push(`⚠️ <strong>${wName} (${masteryName}):</strong> <strong>0/10 invested!</strong> You are wielding this weapon without any mastery damage/accuracy bonus!`);
                    }
                }
            });

            weaponAuditHtml = `
                <div class="advice-item ${weaponWarnings.length === 0 ? 'success' : 'warning'}">
                    <strong>🎯 Equipped Weapon Mastery Match:</strong><br>
                    ${weaponMatches.map(m => `<div>✅ ${m}</div>`).join('')}
                    ${weaponWarnings.map(w => `<div>${w}</div>`).join('')}
                </div>
            `;
        }

        // Bank investment advice
        let bankAdviceHtml = '';
        if (bankRank >= 10) {
            bankAdviceHtml = `<div class="advice-item success"><strong>✅ Bank Investment (10/10):</strong> Maxed! Compounding your $2B bank investment at Torn's peak passive yield.</div>`;
        } else {
            const missing = 55 - meritsForRank(bankRank);
            bankAdviceHtml = `
                <div class="advice-item warning">
                    <strong>⚠️ Bank Investment (${bankRank}/10):</strong> Bank merits give the highest guaranteed passive income ROI in Torn. ${missing} merits needed to max 10/10 (generates +$50M–$80M extra profit per 3-month cycle on $2B).
                </div>
            `;
        }

        // Life and Crit advice
        let lifeAdviceHtml = `
            <div class="advice-item ${lifeRank >= 10 ? 'success' : 'warning'}">
                <strong>❤️ Max Life Merits (${lifeRank}/10):</strong> ${lifeRank > 0 ? `+${lifeRank * 5}% Maximum Life` : '0/10 invested'}. 
                ${lifeRank < 10 
                    ? `⚠️ You are missing <strong>${55 - meritsForRank(lifeRank)} merits</strong> to max Life. In Ranked Wars, this is the difference between surviving a primary headshot or getting 1-shot into the hospital!` 
                    : 'Maxed at +50% Life! Elite Ranked War survivability.'}
            </div>
        `;

        let critAdviceHtml = `
            <div class="advice-item ${critRank >= 10 ? 'success' : 'warning'}">
                <strong>💥 Critical Hit Rate (${critRank}/10):</strong> ${critRank > 0 ? `+${critRank}% Critical Hit Rate` : '0/10 invested'}. 
                ${critRank < 10 
                    ? `Critical hits ignore a percentage of enemy armor mitigation. Essential for Ranked Wars against high-defense targets.` 
                    : 'Maxed at +10% Crit! Provides steady armor-piercing damage.'}
            </div>
        `;

        // Stat Passives Audit (Comparing with player stats & selected build)
        const effectiveDexBonus = (dex * (passiveDex * 0.03));
        const effectiveSpdBonus = (spd * (passiveSpd * 0.03));
        const effectiveDefBonus = (def * (passiveDef * 0.03));
        const effectiveStrBonus = (str * (passiveStr * 0.03));

        let passiveTip = '';
        if (goal.includes('dex')) {
            passiveTip = `For your Dexterity Whore build, maxing <strong>Passive Dexterity (10/10)</strong> and <strong>Passive Speed</strong> yields massive evasion and turn control in wars!`;
        } else if (goal.includes('def')) {
            passiveTip = `For your Defense Tank build, maxing <strong>Passive Defense (10/10)</strong> and <strong>Passive Strength</strong> maximizes damage reduction and counter-punches!`;
        } else if (goal.includes('str')) {
            passiveTip = `For your Strength Whore build, maxing <strong>Passive Strength (10/10)</strong> ensures devastating one-shot KO power in wars!`;
        } else if (goal.includes('spd')) {
            passiveTip = `For your Speed Whore build, maxing <strong>Passive Speed (10/10)</strong> guarantees 100% hit rate and uncounterable stealth hits!`;
        } else {
            passiveTip = `For your Balanced Titan build, distribute merits evenly across combat passives or focus on your highest stat pool for maximum effective combat multipliers.`;
        }

        let statPassiveHtml = `
            <div class="advice-item">
                <strong>📈 Combat Stat Passive Trees (+3% per rank):</strong><br>
                • <strong>Evasion / Dexterity (${passiveDex}/10):</strong> +${passiveDex * 3}% passive Dex (${passiveDex > 0 ? `+${formatShortNumber(effectiveDexBonus)} stats in combat` : '0 invested'}).<br>
                • <strong>Speed (${passiveSpd}/10):</strong> +${passiveSpd * 3}% passive Spd (${passiveSpd > 0 ? `+${formatShortNumber(effectiveSpdBonus)} stats in combat` : '0 invested'}).<br>
                • <strong>Defense (${passiveDef}/10):</strong> +${passiveDef * 3}% passive Def (${passiveDef > 0 ? `+${formatShortNumber(effectiveDefBonus)} stats in combat` : '0 invested'}).<br>
                • <strong>Strength (${passiveStr}/10):</strong> +${passiveStr * 3}% passive Str (${passiveStr > 0 ? `+${formatShortNumber(effectiveStrBonus)} stats in combat` : '0 invested'}).<br>
                <span style="font-size:11px; color:#aaa;">*Tip: ${passiveTip}</span>
            </div>
        `;

        // Refundable merits alert box
        let refundHtml = '';
        if (wastedMeritsTotal > 0) {
            refundHtml = `
                <div class="advice-item warning" style="border: 1px solid var(--accent-red); background: rgba(244, 67, 54, 0.12);">
                    <strong>🔄 Merit Reset Recommended: ${wastedMeritsTotal} Merits Trapped in Low-Impact Trees!</strong><br>
                    You have invested points in non-combat trees: <em>${wastedItemsList.join(', ')}</em>.<br>
                    👉 <strong>Action:</strong> Use a Merit Reset (in your Preferences/Points menu). You can immediately redirect these <strong>${wastedMeritsTotal} merits</strong> to max <strong>Life (10/10)</strong>, <strong>Critical Hit (10/10)</strong>, or your <strong>Primary Weapon Mastery</strong>!
                </div>
            `;
        }

        // Render everything with S-Tier checklist
        container.innerHTML = `
            ${refundHtml}
            ${weaponAuditHtml}
            ${lifeAdviceHtml}
            ${critAdviceHtml}
            ${bankAdviceHtml}
            ${statPassiveHtml}

            <!-- Veteran Merit Priority Tier List -->
            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    🏆 Veteran Combat Merit Priority Hierarchy (S-Tier to F-Tier)
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Mandatory)</span> 
                        <strong>Life (10/10)</strong> • <strong>Bank Investment (10/10)</strong> • <strong>Critical Hit (10/10)</strong> • <strong>Equipped Primary Weapon Mastery (10/10)</strong>
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (High Impact)</span> 
                        <strong>Education Length (10/10)</strong> • <strong>Primary Stat Passive (10/10 on highest stat)</strong> • <strong>Secondary Weapon Mastery</strong>
                    </div>
                    <div style="margin-bottom:6px;">
                        <span class="badge" style="background:var(--accent-blue); color:#fff; font-size:10px;">B-TIER (Situational)</span> 
                        <strong>Temporary Weapon Mastery (for Flash/Smoke)</strong> • <strong>Addiction Mitigation</strong> • <strong>Nerve Bar (for faction OC/chains)</strong>
                    </div>
                    <div>
                        <span class="badge" style="background:#555; color:#fff; font-size:10px;">F-TIER (Reset Targets)</span> 
                        <span style="color:#aaa;">Awareness, Mugging Amount, Master Thief, Stealth, Brawn (Pull these for combat resets!)</span>
                    </div>
                </div>
            </div>
        `;
    }

    // 4. Armor & Weapon Advice (Dynamically reactive to Selected Goal / Archetype!)
    function renderGearAdvice(equipment, d, selectedGoal) {
        if (!d) return;
        equipment = equipment || [];
        const goal = selectedGoal || (document.getElementById('goalSelector')?.value || 'hank_dex');
        const armorPieces = equipment.filter(e => e.type === 'Armor');
        let armorAdviceHtml = '';

        if (armorPieces.length < 5) {
            armorAdviceHtml += `
                <div class="advice-item warning">
                    <strong>Missing Armor Slots (${armorPieces.length}/5):</strong> You have empty armor slots! Cover all 5 slots (Helmet, Body, Pants, Gloves, Boots) so enemies don't land unmitigated hits on naked body parts.
                </div>
            `;
        }

        const sortedArmor = [...armorPieces].sort((a, b) => (a.stats?.armor || 0) - (b.stats?.armor || 0));

        // Archetype profile rules
        let archetypeTitle = '💨 Dexterity Rogue (Evasion Whore)';
        let helmetAdvice = '';
        let armorMetaAdvice = '';
        let weaponMetaAdvice = '';
        let tempMetaAdvice = '';
        let ammoMetaAdvice = '';

        if (goal === 'hank_def' || goal === 'baldr_def') {
            archetypeTitle = '🛡️ Defense Tank (Mr. Isoyama / Def Primary)';
            helmetAdvice = `
                <div class="advice-item success">
                    <strong>Helmet Strategy for Defense Tanks:</strong> You do <strong>NOT</strong> need a Riot Helmet! Pepper Spray debuffs Dexterity (which is your dump stat anyway). Wear a high base armor <strong>Assault Helmet (47+ armor)</strong> or endgame <strong>Dune Helmet</strong> for raw mitigation.
                </div>
            `;
            armorMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Tank God)</span> 
                    <strong>Vanguard Armor:</strong> Life Regeneration pairs insanely well with low incoming damage • <strong>Dune Armor:</strong> Peak base armor.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Ranked War Standard)</span> 
                    <strong>Full Assault Armor (45+ Armor)</strong> with <em>Impenetrable</em> (bullets) and <em>Impregnable</em> (melee). Absorbs chip damage down to 0–50 HP.
                </div>
            `;
            weaponMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Tank Finishers)</span> 
                    <strong>Execute:</strong> Because tanks fight long duels, Execute ends wounded enemies without risking late surprises • <strong>Bloodlust / Life Steal:</strong> Recovers 20–30% HP per hit while enemy deals double-digit damage.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Armor Bypass)</span> 
                    <strong>Penetrate:</strong> Defense tanks often dump Strength, so Penetrate ensures you don't hit enemy armor for zero damage.
                </div>
            `;
            tempMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-blue); color:#fff; font-size:10px;">MANDATORY TANK TEMP</span> 
                    <strong>Smoke Grenade (+25% to +35% Defense):</strong> Stacks on top of your massive Defense pool, rendering enemy attacks completely useless.
                </div>
            `;
            ammoMetaAdvice = `
                <strong style="color:var(--accent-orange);">Piercing Ammo:</strong> Tanks must run Piercing rounds on primary weapons to compensate for dumped Strength.
            `;
        } else if (goal === 'hank_str' || goal === 'baldr_str') {
            archetypeTitle = '🏋️ Strength One-Puncher (Gym 3000 / Str Whore)';
            helmetAdvice = `
                <div class="advice-item">
                    <strong>Helmet Strategy for Strength Whore:</strong> Focus on max damage mitigation with <strong>Assault</strong> or <strong>Marauder Helmet</strong>. You will trade blows while lining up your knockout shot.
                </div>
            `;
            armorMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Heavy Brawler)</span> 
                    <strong>Marauder Armor:</strong> Heavy mitigation and melee synergy • <strong>Dune Armor:</strong> Maximum survival.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Standard Brawler)</span> 
                    <strong>Assault Armor (45+ Armor):</strong> Solid all-round defense with <em>Impenetrable</em> to absorb incoming fire.
                </div>
            `;
            weaponMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Knockout Weapons)</span> 
                    <strong>Powerful (+XX% Damage) Armalite:</strong> One burst can 1-shot enemies • <strong>Wither / Irradiate:</strong> Shreds enemy stats on first contact.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Heavy Melee)</span> 
                    <strong>Macana / Diamond Bladed Knife:</strong> High damage melee finishers.
                </div>
            `;
            tempMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">MANDATORY ACCURACY TEMP</span> 
                    <strong>Flash Grenade (-Speed) or Tear Gas (-80% Enemy Speed):</strong> Since you dump Speed, throwing Tear Gas / Flash on Turn 1 allows your heavy hits to connect 100%!
                </div>
            `;
            ammoMetaAdvice = `
                <strong style="color:var(--accent-blue);">Tracer Ammo (MANDATORY):</strong> Essential to compensate for dumped Speed and guarantee your devastating hits land.
            `;
        } else if (goal === 'hank_spd' || goal === 'baldr_spd') {
            archetypeTitle = '⚡ Speed Demon (Total Rebound / Spd Whore)';
            helmetAdvice = `
                <div class="advice-item">
                    <strong>Helmet Strategy for Speed Assassin:</strong> Pair <strong>Combat</strong> or <strong>Vanguard Helmet</strong>. You rely on 100% accuracy and stealth to dismantle opponents before they react.
                </div>
            `;
            armorMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Assassin Set)</span> 
                    <strong>Vanguard Armor:</strong> Light, agile, and offers life recovery.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Agile Standard)</span> 
                    <strong>Combat / Assault Armor (43+ Armor):</strong> Keeps incoming bullet damage mitigated during stealth runs.
                </div>
            `;
            weaponMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Crit & Bleed Synergy)</span> 
                    <strong>Deadeye (+Crit Damage):</strong> Speed demons have 100% hit rates; stacking Deadeye + 10/10 Crit merits bypasses low base Strength!
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Multi-Hit & Fast SMG)</span> 
                    <strong>BT MP9 / Enfield with Expose:</strong> Ensures 30–50% crit rate throughout the duel.
                </div>
            `;
            tempMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">MANDATORY SHUTDOWN TEMP</span> 
                    <strong>Pepper Spray (-80% Enemy Dex):</strong> Completely invalidates evasion rogues, ensuring quick 10-turn stealth kills.
                </div>
            `;
            ammoMetaAdvice = `
                <strong style="color:var(--accent-red);">Hollow Point & Incendiary Ammo:</strong> Fixes dumped Strength by adding flat damage multipliers and fire burn.
            `;
        } else if (goal === 'pure_balanced') {
            archetypeTitle = '🛡️ Pure Balanced Titan (1:1:1:1 Equalizer — Zero Liabilities)';
            helmetAdvice = `
                <div class="advice-item success">
                    <strong>Helmet Strategy for Balanced Builds:</strong> Since you have both solid Defense and Dexterity, wear an <strong>Assault Helmet (47+ armor)</strong> or <strong>Dune Helmet</strong> for maximum mitigation, or a <strong>Tactical Riot Helmet</strong> if facing heavy Pepper Spray in Ranked Wars.
                </div>
            `;
            armorMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Balanced Titan)</span> 
                    <strong>Dune Armor / Vanguard Armor:</strong> High all-around mitigation and regeneration make a 1:1:1:1 build nearly impossible to counter.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (War Standard)</span> 
                    <strong>Assault Armor (45+ Armor)</strong> with <em>Impenetrable</em> and <em>Impregnable</em>.
                </div>
            `;
            weaponMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Versatile Combat)</span> 
                    <strong>Execute Armalite / Powerful Enfield:</strong> You don't have dumped stats, so standard high-damage and Execute primary weapons excel.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Stat Drain)</span> 
                    <strong>Wither / Irradiate / Plunder:</strong> Capitalize on balanced endurance by decaying the enemy's specialist stat.
                </div>
            `;
            tempMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">BALANCED TEMP STRATEGY</span> 
                    <strong>Flash Grenade / Smoke Grenade:</strong> Throw Flash against high-speed opponents or Smoke against high-strength opponents to tilt the duel instantly in your favor.
                </div>
            `;
            ammoMetaAdvice = `
                <strong style="color:var(--accent-green);">Special Ammo Versatility:</strong> Use <strong>Piercing</strong> against tanks or <strong>Hollow Point</strong> against rogues; your balanced stats allow full ammo flexibility!
            `;
        } else {
            // Default: Dex Whore (baldr_dex, hank_dex, or auto-selected Dex)
            archetypeTitle = '💨 Dexterity Rogue (The Elites / Dex Whore)';
            helmetAdvice = `
                <div class="advice-item success">
                    <strong>Tactical Riot Helmet (MANDATORY for Dex):</strong> Grants 100% immunity to <em>Pepper Spray (-80% Dex)</em> and <em>Tear Gas</em>. A single Pepper Spray ruins an evasion build without this helmet!
                </div>
            `;
            armorMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Evasion Armor)</span> 
                    <strong>Dune Armor:</strong> Peak base armor • <strong>Vanguard Armor:</strong> Life recovery for close duels.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Ranked War Standard)</span> 
                    <strong>Assault Armor (45+ Armor) with Impenetrable:</strong> Bullet reduction is your safety net for the 5–10% of enemy shots that sneak past your dodge.
                </div>
            `;
            weaponMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">S-TIER (Burst Lethality)</span> 
                    <strong>Powerful Armalite / Enfield:</strong> Massive clip size and raw +damage to blow enemies away before lucky hits land • <strong>Deadeye:</strong> Lethal crit bursts.
                </div>
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-green); color:#000; font-size:10px;">A-TIER (Secondary Openers)</span> 
                    <strong>Expose Secondary:</strong> Raises crit chance before swapping to melee.
                </div>
            `;
            tempMetaAdvice = `
                <div style="margin-bottom:6px;">
                    <span class="badge" style="background:var(--accent-orange); color:#000; font-size:10px;">MANDATORY EVASION TEMPS</span> 
                    <strong>Flash Grenade (-20% to -35% Enemy Speed):</strong> Keeps your evasion advantage intact against fast opponents.
                </div>
            `;
            ammoMetaAdvice = `
                <strong style="color:var(--accent-red);">Hollow Point Ammo:</strong> Turns high-accuracy hits into lethal burst damage.
            `;
        }

        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const isDexBuild = (goal === 'hank_dex' || goal === 'baldr_dex' || (goal === 'baldr_ratio' && (bs.dexterity || 0) >= Math.max(bs.strength || 0, bs.defense || 0)));

        // When evaluating weakest piece to upgrade, exclude Tactical Riot Helmet on Dex builds!
        const nonRiotPieces = armorPieces.filter(a => !(isDexBuild && a.name.includes('Riot Helmet')));
        const weakest = [...nonRiotPieces].sort((a, b) => (a.stats?.armor || 0) - (b.stats?.armor || 0))[0];

        if (weakest) {
            const wArmor = weakest.stats?.armor || 0;
            if (weakest.name.includes('Assault')) {
                armorAdviceHtml += `
                    <div class="advice-item">
                        <strong>Solid Mid-High Tier Set:</strong> Your Assault pieces (${wArmor}+ armor) are solid. Next step over time is <strong>Combat Armor</strong> (higher base armor) or <strong>Vanguard / Marauder</strong>.
                    </div>
                `;
            } else {
                armorAdviceHtml += `
                    <div class="advice-item">
                        <strong>Immediate Upgrade Target:</strong> Replace <strong>${weakest.name} (${wArmor} armor)</strong> with an <strong>Assault piece (45+ armor)</strong> with <em>Impenetrable</em> or <em>Impregnable</em>.
                    </div>
                `;
            }
        }

        // Add dynamically tailored archetype card
        armorAdviceHtml += `
            ${helmetAdvice}
            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px; display:flex; justify-content:space-between;">
                    <span>🛡️ Ranked War Armor Meta for: ${archetypeTitle}</span>
                    <span style="font-size:11px; color:#aaa;">Tailored Strategy</span>
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    ${armorMetaAdvice}
                    <div>
                        <span class="badge" style="background:#555; color:#fff; font-size:10px;">F-TIER (Scrap)</span> 
                        <span style="color:#aaa;">Heavy Armor, Liquid Body Armor, Flexible Body Armor, Motorcycle Helmet.</span>
                    </div>
                </div>
            </div>
        `;
        document.getElementById('defenseAdvice').innerHTML = armorAdviceHtml;

        // Weapons Audit & RW Bonus Hierarchy
        const wepExp = d.weaponexp || [];
        const mastered = wepExp.filter(w => w.exp >= 90).map(w => w.name);
        const equippedWeapons = equipment.filter(e => e.type === 'Weapon' && e.sub_type !== 'Temporary');

        let wepHtml = `
            <div class="advice-item">
                <strong>Mastered Weapons (90%+ Exp):</strong> ${mastered.length > 0 ? mastered.join(', ') : 'None yet.'}
            </div>
        `;

        if (equippedWeapons.length > 0) {
            const wNames = equippedWeapons.map(w => {
                const bonuses = (w.bonuses || []).map(b => `${b.title} ${b.value}%`).join(', ');
                return `<strong>${w.name}</strong> (${w.sub_type || 'Weapon'}${bonuses ? ` — ★ ${bonuses}` : ''})`;
            }).join('<br>');
            wepHtml += `
                <div class="advice-item">
                    <strong>Equipped Arsenal:</strong><br>${wNames}
                </div>
            `;
        }

        // Tailored Weapon, Temp, and Ammo card
        wepHtml += `
            <div style="margin-top:14px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    ⚔️ Ranked War Weapon & Cache Bonus Meta for: ${archetypeTitle}
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    ${weaponMetaAdvice}
                    <div style="margin-top:4px;">
                        <span class="badge" style="background:#555; color:#fff; font-size:10px;">F-TIER</span> 
                        <span style="color:#aaa;">Slow on low-accuracy guns, Conserve, Disarm.</span>
                    </div>
                </div>
            </div>

            <!-- Tailored Turn 1 Temporary Weapon Protocol -->
            <div style="margin-top:12px; background:#15151a; border:1px solid #2e2e38; border-radius:8px; padding:12px;">
                <div style="font-weight:bold; font-size:13px; color:var(--accent-orange); margin-bottom:8px;">
                    💣 "Turn 1" Temporary Item Protocol for ${archetypeTitle}
                </div>
                <div style="font-size:12px; line-height:1.6;">
                    ${tempMetaAdvice}
                    <div style="margin-top:6px; font-size:12px;">
                        <strong>Optimal Ammo for this Build:</strong> ${ammoMetaAdvice}
                    </div>
                </div>
            </div>
        `;
        document.getElementById('weaponAdvice').innerHTML = wepHtml;
    }

    // 5. Gym Unlock Gate (<100M) & Dynamic Goal Generator
    function updateGoalRoadmap() {
        if (!globalPlayerData) return;

        const d = globalPlayerData;
        const bs = d.strength !== undefined ? d : (d.battlestats || {});
        const str = bs.strength || 0;
        const def = bs.defense || 0;
        const spd = bs.speed || 0;
        const dex = bs.dexterity || 0;
        const total = str + def + spd + dex;

        const goal = document.getElementById('goalSelector').value;
        const titleEl = document.getElementById('goalTitle');
        const adviceEl = document.getElementById('goalAdvice');
        const unlockBanner = document.getElementById('gymUnlockBanner');

        // Check if player has unlocked specialist gyms (< 100M total stats)
        if (total < 100e6) {
            unlockBanner.innerHTML = `
                <div class="alert-banner alert-info">
                    ℹ️ <div><strong>Gym Unlock Gate (<100M Stats):</strong> You currently have ${formatShortNumber(total)} total battle stats. Specialist gyms require unlocking <strong>Cha Cha's</strong> and <strong>George's Gym</strong> first. Maintain a balanced profile (1:1:1:1) in your highest available gym until George's is unlocked!</div>
                </div>
            `;
        } else {
            unlockBanner.innerHTML = '';
        }

        let html = '';

        // =========================================================================
        // GymIQ Mathematical Engine: Dynamic Deficit, Ratio Targets & "Train Next"
        // =========================================================================
        let targetPercents = { str: 0.25, def: 0.25, spd: 0.25, dex: 0.25 };
        let ratioLabel = '';
        let gymPathText = '';
        let specialNotes = '';

        if (goal === 'baldr_dex') {
            titleEl.innerHTML = '💨 Baldr\'s Dex Whore (100 Dex : 80 Spd : 72 Str : 72 Def — 31% / 25% / 22% / 22%)';
            targetPercents = { dex: 0.31, spd: 0.25, str: 0.22, def: 0.22 };
            ratioLabel = '100 (Dex) : 80 (Spd) : 72 (Str) : 72 (Def)';
            gymPathText = '• Dex Primary in The Elites (8.0 dots) • Spd Secondary in Frontline Fitness (7.5 dots) • Str & Def in George\'s Gym (7.3 dots).';
            specialNotes = 'Dex Whore with Baldr safety: High evasion and specialist gym training, while keeping Def & Str at a resilient 22% baseline to prevent easy one-shots.';

        } else if (goal === 'baldr_def') {
            titleEl.innerHTML = '🛡️ Baldr\'s Defense Tank (100 Def : 80 Str : 72 Spd : 72 Dex — 31% / 25% / 22% / 22%)';
            targetPercents = { def: 0.31, str: 0.25, spd: 0.22, dex: 0.22 };
            ratioLabel = '100 (Def) : 80 (Str) : 72 (Spd) : 72 (Dex)';
            gymPathText = '• Def Primary in Mr. Isoyama\'s (8.0 dots) • Str Secondary in Frontline Fitness (7.5 dots) • Spd & Dex in George\'s Gym (7.3 dots).';
            specialNotes = 'Def Tank with Baldr safety: Heavy damage mitigation in Isoyama, while preserving 22% Speed & Dexterity so your attacks still land reliably.';

        } else if (goal === 'baldr_str') {
            titleEl.innerHTML = '🏋️ Baldr\'s Strength Whore (100 Str : 80 Def : 72 Dex : 72 Spd — 31% / 25% / 22% / 22%)';
            targetPercents = { str: 0.31, def: 0.25, dex: 0.22, spd: 0.22 };
            ratioLabel = '100 (Str) : 80 (Def) : 72 (Dex) : 72 (Spd)';
            gymPathText = '• Str Primary in Gym 3000 (8.0 dots) • Def Secondary in Balboa\'s Gym (7.5 dots) • Dex & Spd in George\'s Gym (7.3 dots).';
            specialNotes = 'Knockout damage without a fatal speed liability: Crushes high-armor tanks while keeping enough Speed & Dex to evade opponents.';

        } else if (goal === 'baldr_spd') {
            titleEl.innerHTML = '⚡ Baldr\'s Speed Whore (100 Spd : 80 Def : 72 Dex : 72 Str — 31% / 25% / 22% / 22%)';
            targetPercents = { spd: 0.31, def: 0.25, dex: 0.22, str: 0.22 };
            ratioLabel = '100 (Spd) : 80 (Def) : 72 (Dex) : 72 (Str)';
            gymPathText = '• Spd Primary in Total Rebound (8.0 dots) • Def Secondary in Balboa\'s Gym (7.5 dots) • Dex & Str in George\'s Gym (7.3 dots).';
            specialNotes = 'Anti-Dex assassin with Baldr safety: Unlocks Total Rebound, hits 100% of Turn 1 attacks with stealth, while keeping high Def & Str.';

        } else if (goal === 'baldr_ratio') {
            titleEl.innerHTML = '⚖️ Baldr\'s Ratio (100 : 80 : 72 : 72 — 31% Primary / 25% Secondary / 22% & 22% Bottom)';
            // Identify player's highest two stats as primary & secondary
            const statPairs = [
                { name: 'Strength', key: 'str', val: str },
                { name: 'Defense', key: 'def', val: def },
                { name: 'Speed', key: 'spd', val: spd },
                { name: 'Dexterity', key: 'dex', val: dex }
            ].sort((a, b) => b.val - a.val);

            targetPercents[statPairs[0].key] = 0.31;
            targetPercents[statPairs[1].key] = 0.25;
            targetPercents[statPairs[2].key] = 0.22;
            targetPercents[statPairs[3].key] = 0.22;

            ratioLabel = `100 (${statPairs[0].name}) : 80 (${statPairs[1].name}) : 72 (${statPairs[2].name}) : 72 (${statPairs[3].name})`;
            gymPathText = `1. Primary (${statPairs[0].name}) in 8.0-dot specialist gym • 2. Secondary (${statPairs[1].name}) in 7.5-dot combo gym (e.g. Frontline / Balboa's) • 3. Lower stats in George's Gym (7.3 dots).`;
            specialNotes = `Baldr's build gives you high-dot specialist gym gains while keeping bottom two stats high enough (~22% each) that PvP opponents cannot easily exploit a weakness.`;

        } else if (goal === 'pure_balanced') {
            titleEl.innerHTML = '🛡️ Pure Balanced Titan (1 : 1 : 1 : 1 — Equal 25% All Stats)';
            targetPercents = { str: 0.25, def: 0.25, spd: 0.25, dex: 0.25 };
            ratioLabel = '1 : 1 : 1 : 1 (Equal 25% across all 4 stats)';
            gymPathText = 'Train all stats in George\'s Gym (7.3 dots) or highest available multi-stat gym.';
            specialNotes = 'Sacrifices specialist gym access in exchange for having zero stat liabilities in Ranked Wars.';

        } else if (goal === 'gymi_heavy_specialist') {
            titleEl.innerHTML = '🥊 GymIQ Heavy Specialist (100 : 70 : 70 : 70 — 32.2% Primary / 22.6% Balanced Others)';
            const statPairs = [
                { name: 'Strength', key: 'str', val: str },
                { name: 'Defense', key: 'def', val: def },
                { name: 'Speed', key: 'spd', val: spd },
                { name: 'Dexterity', key: 'dex', val: dex }
            ].sort((a, b) => b.val - a.val);

            targetPercents[statPairs[0].key] = 0.322;
            targetPercents[statPairs[1].key] = 0.226;
            targetPercents[statPairs[2].key] = 0.226;
            targetPercents[statPairs[3].key] = 0.226;

            ratioLabel = `100 (${statPairs[0].name}) : 70 : 70 : 70`;
            gymPathText = `Primary (${statPairs[0].name}) in 8.0-dot specialist gym (requires 1.25x ratio over second-highest). Others in George's Gym.`;
            specialNotes = `Safe specialist setup: unlocks an 8.0 dot gym while keeping all three support stats completely equal at 70% of primary.`;

        } else if (goal === 'hank_dex') {
            titleEl.innerHTML = '💨 Hank\'s Dex Whore (100 : 80 : 80 : 28 Max — ~35% Dex / 28% Spd / 28% Str / ~9% Def)';
            targetPercents = { dex: 0.35, spd: 0.28, str: 0.28, def: 0.09 };
            ratioLabel = '100 (Dex) : 80 (Spd) : 80 (Str) : 28 max (Def dump)';
            gymPathText = '• Dex in The Elites (8.0 dots) • Str & Spd in Frontline Fitness (7.5 dots) • Def dumped at George\'s.';
            specialNotes = 'Unlocks THREE high-dot specialist gyms! Defense is dumped, so you rely on evasion and high-armor sets.';

        } else if (goal === 'hank_def') {
            titleEl.innerHTML = '🛡️ Hank\'s Defense Tank (100 : 80 : 80 : 28 Max — ~35% Def / 28% Str / 28% Spd / ~9% Dex)';
            targetPercents = { def: 0.35, str: 0.28, spd: 0.28, dex: 0.09 };
            ratioLabel = '100 (Def) : 80 (Str) : 80 (Spd) : 28 max (Dex dump)';
            gymPathText = '• Def in Mr. Isoyama\'s (8.0 dots) • Str & Spd in Frontline Fitness (7.5 dots) • Dex dumped at George\'s.';
            specialNotes = 'Unlocks Isoyama + Frontline. Extreme damage mitigation: enemy hits deal chip damage, breaking chains in Ranked Wars.';

        } else if (goal === 'hank_str') {
            titleEl.innerHTML = '🏋️ Hank\'s Strength Whore (100 : 80 : 80 : 28 Max — ~35% Str / 28% Def / 28% Dex / ~9% Spd)';
            targetPercents = { str: 0.35, def: 0.28, dex: 0.28, spd: 0.09 };
            ratioLabel = '100 (Str) : 80 (Def) : 80 (Dex) : 28 max (Spd dump)';
            gymPathText = '• Str in Gym 3000 (8.0 dots) • Def & Dex in Balboa\'s Gym (7.5 dots) • Spd dumped at George\'s.';
            specialNotes = 'Unlocks Gym 3000 + Balboa\'s. One-punch armor shredder. Pair with Tracer Ammo or Flash/Tear Gas to offset low Speed.';

        } else if (goal === 'hank_spd') {
            titleEl.innerHTML = '⚡ Hank\'s Speed Demon (100 : 80 : 80 : 28 Max — ~35% Spd / 28% Def / 28% Dex / ~9% Str)';
            targetPercents = { spd: 0.35, def: 0.28, dex: 0.28, str: 0.09 };
            ratioLabel = '100 (Spd) : 80 (Def) : 80 (Dex) : 28 max (Str dump)';
            gymPathText = '• Spd in Total Rebound (8.0 dots) • Def & Dex in Balboa\'s Gym (7.5 dots) • Str dumped at George\'s.';
            specialNotes = 'Unlocks Total Rebound + Balboa\'s. Guaranteed 100% accuracy, extreme stealth, and anti-Dex assassin capability.';

        } else if (goal === 'se_rush') {
            const dominantStatName = str >= Math.max(def, spd, dex) ? 'Strength' :
                                    def >= Math.max(spd, dex) ? 'Defense' :
                                    spd >= dex ? 'Speed' : 'Dexterity';
            const highestStat = Math.max(str, def, spd, dex);
            const needed = Math.max(0, 1.5e9 - highestStat);

            titleEl.innerHTML = `🚀 Stat Enhancer (SE) Rush: Push ${dominantStatName} to 1.5 Billion`;
            html = `
                <div class="advice-item">
                    <strong>The 1.5B Milestone:</strong> Your highest stat is <strong>${dominantStatName} (${formatShortNumber(highestStat)})</strong>. You need <strong>${formatShortNumber(needed)}</strong> more stats before SEs become cost-optimal.
                </div>
                <div class="advice-item">
                    <strong>Single-Stat Focus:</strong> Ignore other 3 stats. Dump 100% of your energy (3x Xanax + Refills + Cans) into ${dominantStatName}. Once it hits 1.5B, each SE gives +15,000,000+ per use!
                </div>
            `;
        }

        if (goal !== 'se_rush') {
            // Compute GymIQ Target Numbers and Deficits
            const stats = [
                { name: 'Strength', key: 'str', current: str, target: Math.round(total * targetPercents.str), pct: (targetPercents.str * 100).toFixed(1) },
                { name: 'Defense', key: 'def', current: def, target: Math.round(total * targetPercents.def), pct: (targetPercents.def * 100).toFixed(1) },
                { name: 'Speed', key: 'spd', current: spd, target: Math.round(total * targetPercents.spd), pct: (targetPercents.spd * 100).toFixed(1) },
                { name: 'Dexterity', key: 'dex', current: dex, target: Math.round(total * targetPercents.dex), pct: (targetPercents.dex * 100).toFixed(1) }
            ];

            // Calculate deficit: target - current
            stats.forEach(s => {
                s.deficit = s.target - s.current;
                s.currentPct = ((s.current / total) * 100).toFixed(1);
            });

            // Find largest deficit -> GymIQ "Train Next" recommendation
            const trainNextStat = [...stats].sort((a, b) => b.deficit - a.deficit)[0];

            html = `
                <!-- GymIQ "Train Next" Highlight Card -->
                <div style="background: linear-gradient(135deg, rgba(255, 153, 0, 0.15), rgba(0, 0, 0, 0.5)); border: 2px solid var(--accent-orange); border-radius: 8px; padding: 14px; margin-bottom: 14px; display:flex; justify-content:space-between; align-items:center; flex-wrap:wrap; gap:10px;">
                    <div>
                        <div style="font-size:11px; text-transform:uppercase; letter-spacing:1px; color:#ff9900; font-weight:bold;">⚡ GymIQ Recommendation — Train Next:</div>
                        <div style="font-size:20px; font-weight:bold; color:#fff; margin-top:2px;">
                            Train <span style="color:var(--accent-orange);">${trainNextStat.name}</span>
                            ${trainNextStat.deficit > 0 ? `<span style="font-size:13px; color:#ff9800; font-weight:normal;">(Deficit: ~${formatShortNumber(trainNextStat.deficit)} stats behind target)</span>` : '<span style="font-size:13px; color:var(--accent-green); font-weight:normal;">(Target Reached!)</span>'}
                        </div>
                    </div>
                    <div style="text-align:right;">
                        <span class="badge" style="background:var(--accent-orange); color:#000; font-size:11px; padding:6px 12px;">Target: ${trainNextStat.pct}% (${formatShortNumber(trainNextStat.target)})</span>
                    </div>
                </div>

                <div class="advice-item">
                    <strong>Target Ratio Profile:</strong> <code>${ratioLabel}</code><br>
                    ${specialNotes}
                </div>

                <!-- Stat Target & Deficit Breakdown Table -->
                <div class="advice-item" style="padding:0; border:none; margin-top:10px; overflow-x:auto;">
                    <table style="width:100%; border-collapse:collapse; font-size:12px; background:#16161c; border-radius:6px; overflow:hidden;">
                        <thead>
                            <tr style="background:#22222b; color:#aaa; text-align:left;">
                                <th style="padding:8px 12px;">Stat</th>
                                <th style="padding:8px 12px;">Current (Actual %)</th>
                                <th style="padding:8px 12px;">Target (Goal %)</th>
                                <th style="padding:8px 12px;">GymIQ Gap / Deficit</th>
                                <th style="padding:8px 12px;">Status</th>
                            </tr>
                        </thead>
                        <tbody>
                            ${stats.map(s => {
                                const isDeficit = s.deficit > 0;
                                return `
                                    <tr style="border-top:1px solid #282834;">
                                        <td style="padding:8px 12px; font-weight:bold; color:#fff;">${s.name}</td>
                                        <td style="padding:8px 12px;">${formatShortNumber(s.current)} <span style="color:#888;">(${s.currentPct}%)</span></td>
                                        <td style="padding:8px 12px; color:var(--accent-orange); font-weight:bold;">${formatShortNumber(s.target)} <span style="color:#aaa;">(${s.pct}%)</span></td>
                                        <td style="padding:8px 12px; font-weight:bold; color:${isDeficit ? 'var(--accent-orange)' : 'var(--accent-green)'};">
                                            ${isDeficit ? `-${formatShortNumber(s.deficit)}` : `+${formatShortNumber(Math.abs(s.deficit))} (Surplus)`}
                                        </td>
                                        <td style="padding:8px 12px;">
                                            <span class="badge" style="background:${isDeficit ? '#332200' : '#003311'}; color:${isDeficit ? '#ffaa00' : '#44ff77'}; border:1px solid ${isDeficit ? '#774400' : '#006622'}; font-size:10px;">
                                                ${isDeficit ? 'Needs Training' : 'Optimal'}
                                            </span>
                                        </td>
                                    </tr>
                                `;
                            }).join('')}
                        </tbody>
                    </table>
                </div>

                <div class="advice-item" style="margin-top:12px;">
                    <strong>Gym Path:</strong><br>${gymPathText}
                </div>
            `;
        }

        // Dynamic SE Timing Advice appended to every Goal Roadmap
        const highestStat = Math.max(str, def, spd, dex);
        const dominantName = str >= Math.max(def, spd, dex) ? 'Strength' :
                             def >= Math.max(spd, dex) ? 'Defense' :
                             spd >= dex ? 'Speed' : 'Dexterity';
        const seItemMap = {
            'Strength': 'Boxing Gloves',
            'Defense': 'Dumbbells',
            'Speed': 'Skateboard',
            'Dexterity': 'Parachute'
        };
        const seItem = seItemMap[dominantName];

        if (highestStat >= 1.5e9) {
            html += `
                <div class="advice-item success">
                    <strong>💊 Stat Enhancer (SE) Timing:</strong> Your <strong>${dominantName} (${formatShortNumber(highestStat)})</strong> is above 1.5B! Each <strong>${seItem}</strong> now gives <strong>+${formatShortNumber(highestStat * 0.01)}</strong> stats. Start cycling ${seItem}s whenever you have spare cash/booster cooldown.
                </div>
            `;
        } else {
            const seGap = 1.5e9 - highestStat;
            html += `
                <div class="advice-item">
                    <strong>💊 When to Take Your Next SE (${seItem}):</strong> Wait until your <strong>${dominantName}</strong> hits <strong>1.50B</strong> (you need <strong>${formatShortNumber(seGap)}</strong> more). Below 1.5B, Energy Cans/FHCs + 3x Xanax/day give more stats per dollar than SEs.
                </div>
            `;
        }

        adviceEl.innerHTML = html;

        // Re-render Gear, Weapon, Next Buy, Merits, and Veteran Company advice whenever Goal changes to match archetype!
        renderGearAdvice(globalEquipment, globalPlayerData, goal);
        auditMeritsAndPerks(globalPlayerData, globalEquipment, goal);
        auditVeteranPrep(globalPlayerData, globalAttacks, globalRecentXan, goal);
        updateQuickNextBuy(globalPlayerData, globalEquipment);
    }

        // Export key methods to window & unsafeWindow
        window.startAnalysis = startAnalysis;
        window.copyDiscordSummary = copyDiscordSummary;
        window.scanActiveWarRoster = scanActiveWarRoster;
        window.scoutEnemy = scoutEnemy;
        window.updateGoalRoadmap = updateGoalRoadmap;
        window.switchTwaTab = switchTwaTab;

        targetWindow.startAnalysis = startAnalysis;
        targetWindow.copyDiscordSummary = copyDiscordSummary;
        targetWindow.scanActiveWarRoster = scanActiveWarRoster;
        targetWindow.scoutEnemy = scoutEnemy;
        targetWindow.updateGoalRoadmap = updateGoalRoadmap;
        targetWindow.switchTwaTab = switchTwaTab;

        // Explicitly attach event listeners to tab buttons
        ['war', 'gym', 'gear', 'merits', 'economy'].forEach(tabKey => {
            const btn = document.getElementById('tabBtn-' + tabKey);
            if (btn) {
                btn.onclick = null;
                btn.addEventListener('click', (e) => {
                    e.preventDefault();
                    switchTwaTab(tabKey);
                });
            }
        });

        // Explicitly attach addEventListener to all actionable buttons so they NEVER fail regardless of sandbox isolation
        const analyzeBtn = document.getElementById('analyzeBtn');
        if (analyzeBtn) {
            analyzeBtn.onclick = null;
            analyzeBtn.addEventListener('click', (e) => {
                e.preventDefault();
                console.log('[TornWarAdvisor] Analyze button clicked, starting analysis...');
                startAnalysis();
            });
        }

        const enemyScoutBtn = document.querySelector('button[onclick*=\"scoutEnemy\"]');
        if (enemyScoutBtn) {
            enemyScoutBtn.onclick = null;
            enemyScoutBtn.addEventListener('click', (e) => {
                e.preventDefault();
                scoutEnemy();
            });
        }

        const warScanBtn = document.querySelector('button[onclick*=\"scanActiveWarRoster\"]');
        if (warScanBtn) {
            warScanBtn.onclick = null;
            warScanBtn.addEventListener('click', (e) => {
                e.preventDefault();
                scanActiveWarRoster();
            });
        }

        const goalSelect = document.getElementById('goalSelector');
        if (goalSelect) {
            goalSelect.onchange = null;
            goalSelect.addEventListener('change', () => {
                updateGoalRoadmap();
            });
        }

    }

    // Launch button setup with retry loop for single-page navigations & dynamic DOM
    function ensureLauncher() {
        initLauncher();
        setInterval(initLauncher, 2000);
    }

    if (document.readyState === 'loading') {
        document.addEventListener('DOMContentLoaded', ensureLauncher);
    } else {
        ensureLauncher();
    }
})();
