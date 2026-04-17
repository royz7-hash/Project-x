<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CoinFlip Platform</title>
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=DM+Mono:wght@300;400;500&display=swap" rel="stylesheet">
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --black:#0a0a0a;--white:#f5f5f0;--grey:#1e1e1e;--mid:#555;
  --green:#b8f0b8;--red:#f0b8b8;--yellow:#f0e8b8;--blue:#b8d8f0;--purple:#d4b8f0;
}
body{background:var(--black);color:var(--white);font-family:'DM Mono',monospace;min-height:100dvh;overflow-x:hidden}
body::before{content:'';position:fixed;inset:0;background-image:linear-gradient(rgba(255,255,255,0.018) 1px,transparent 1px),linear-gradient(90deg,rgba(255,255,255,0.018) 1px,transparent 1px);background-size:40px 40px;pointer-events:none;z-index:0}

/* ══ SCREENS ══ */
.screen{position:fixed;inset:0;z-index:10;display:none;overflow-y:auto;padding:2rem 1.5rem}
.screen.active{display:flex;flex-direction:column;align-items:center}
#authScreen{justify-content:center}
#gameScreen,#adminScreen,#masterScreen{padding-top:68px;padding-bottom:3rem;align-items:flex-start;justify-content:flex-start}

/* ══ AUTH ══ */
.auth-box{width:100%;max-width:380px;display:flex;flex-direction:column;gap:1.4rem}
.auth-logo{text-align:center}
.auth-logo h1{font-family:'Bebas Neue',sans-serif;font-size:clamp(3rem,14vw,5rem);letter-spacing:0.1em;line-height:1}
.auth-logo h1 span{color:var(--mid)}
.auth-logo p{font-size:0.6rem;letter-spacing:0.3em;text-transform:uppercase;color:var(--mid);margin-top:0.3rem}
.auth-tabs{display:flex;gap:1px;background:#222;border-radius:4px;overflow:hidden}
.auth-tab{flex:1;padding:0.75rem;background:var(--grey);border:none;color:var(--mid);font-family:'DM Mono',monospace;font-size:0.7rem;letter-spacing:0.2em;text-transform:uppercase;cursor:pointer;transition:all 0.2s}
.auth-tab.active{background:var(--white);color:var(--black)}
.auth-form{display:flex;flex-direction:column;gap:0.8rem}
.field-label{font-size:0.58rem;letter-spacing:0.2em;text-transform:uppercase;color:var(--mid);display:block;margin-bottom:0.3rem}
.field-input{width:100%;background:#161616;border:1px solid #2a2a2a;color:var(--white);font-family:'DM Mono',monospace;font-size:0.9rem;padding:0.8rem 1rem;border-radius:4px;outline:none;transition:border-color 0.2s}
.field-input:focus{border-color:#555}
.field-input::placeholder{color:#333}
.auth-btn{width:100%;padding:1rem;background:var(--white);color:var(--black);border:none;font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:0.3em;cursor:pointer;border-radius:4px;transition:opacity 0.2s;margin-top:0.3rem}
.auth-btn:hover{opacity:0.85}
.auth-err{font-size:0.65rem;color:var(--red);text-align:center;min-height:1rem;letter-spacing:0.1em}
.auth-divider{display:flex;align-items:center;gap:0.8rem}
.auth-divider::before,.auth-divider::after{content:'';flex:1;height:1px;background:#1e1e1e}
.auth-divider span{font-size:0.55rem;letter-spacing:0.2em;text-transform:uppercase;color:#2a2a2a}
.demo-creds{font-size:0.58rem;color:#2a2a2a;text-align:center;letter-spacing:0.08em;line-height:1.9}
.demo-creds strong{color:#3a3a3a}

/* ══ TOPBAR ══ */
.topbar{position:fixed;top:0;left:0;right:0;z-index:50;background:rgba(10,10,10,0.96);backdrop-filter:blur(14px);border-bottom:1px solid #181818;height:52px;display:none;align-items:center;justify-content:space-between;padding:0 1.2rem}
.topbar.show{display:flex}
.topbar-logo{font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:0.15em;cursor:pointer}
.topbar-logo span{color:var(--mid)}
.topbar-right{display:flex;align-items:center;gap:0.6rem;flex-wrap:nowrap}
.topbar-user{font-size:0.58rem;letter-spacing:0.12em;text-transform:uppercase;color:var(--mid);max-width:90px;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.role-badge{font-size:0.5rem;letter-spacing:0.15em;text-transform:uppercase;padding:0.22rem 0.5rem;border-radius:3px;font-weight:500;flex-shrink:0}
.role-badge.user{background:#1e1e1e;color:#555;border:1px solid #2a2a2a}
.role-badge.admin{background:var(--yellow);color:#1a1000}
.role-badge.master{background:var(--purple);color:#1a0030}
.nav-btn{background:transparent;border:1px solid #2a2a2a;color:#555;font-family:'DM Mono',monospace;font-size:0.52rem;letter-spacing:0.12em;text-transform:uppercase;padding:0.28rem 0.55rem;border-radius:3px;cursor:pointer;transition:all 0.2s;white-space:nowrap}
.nav-btn:hover{border-color:#555;color:#aaa}
.nav-btn.active-nav{background:var(--white);color:var(--black);border-color:var(--white)}

/* ══ SHARED PANEL LAYOUT ══ */
.panel-wrap{position:relative;z-index:1;width:100%;max-width:760px;display:flex;flex-direction:column;gap:1.8rem;padding:0 0.2rem}
.panel-header{display:flex;align-items:flex-start;justify-content:space-between;flex-wrap:wrap;gap:0.8rem;padding-bottom:1rem;border-bottom:1px solid #1a1a1a}
.panel-title{font-family:'Bebas Neue',sans-serif;font-size:2.2rem;letter-spacing:0.12em;line-height:1}
.panel-title span{color:var(--yellow)}
.panel-title span.purple{color:var(--purple)}
.panel-subtitle{font-size:0.58rem;color:var(--mid);letter-spacing:0.15em;margin-top:0.3rem}
.panel-actions{display:flex;gap:0.6rem;align-items:center;flex-wrap:wrap}

/* ══ STAT CARDS ══ */
.stat-grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(130px,1fr));gap:1px;background:#1a1a1a;border-radius:6px;overflow:hidden;border:1px solid #1a1a1a}
.stat-card{background:#111;padding:1rem 1.1rem}
.sc-label{font-size:0.52rem;letter-spacing:0.2em;text-transform:uppercase;color:#333;display:block;margin-bottom:0.4rem}
.sc-val{font-family:'Bebas Neue',sans-serif;font-size:1.7rem;letter-spacing:0.05em;line-height:1}
.sc-val.g{color:var(--green)}.sc-val.r{color:var(--red)}.sc-val.y{color:var(--yellow)}.sc-val.b{color:var(--blue)}.sc-val.p{color:var(--purple)}.sc-val.w{color:var(--white)}
.sc-sub{font-size:0.55rem;color:#333;margin-top:0.2rem}

/* ══ SECTION BLOCK ══ */
.section-block{display:flex;flex-direction:column;gap:0.8rem}
.section-title{font-size:0.58rem;letter-spacing:0.25em;text-transform:uppercase;color:#444;display:flex;align-items:center;gap:0.6rem}
.section-title::after{content:'';flex:1;height:1px;background:#1a1a1a}

/* ══ FORMS ══ */
.form-card{background:#111;border:1px solid #1e1e1e;border-radius:6px;padding:1.1rem;display:flex;flex-direction:column;gap:0.8rem}
.form-row{display:flex;gap:0.6rem;flex-wrap:wrap}
.form-row .fw{flex:1;min-width:120px;display:flex;flex-direction:column}
.adm-input{background:#0a0a0a;border:1px solid #2a2a2a;color:var(--white);font-family:'DM Mono',monospace;font-size:0.8rem;padding:0.55rem 0.75rem;border-radius:4px;outline:none;transition:border-color 0.2s;width:100%}
.adm-input:focus{border-color:#555}
.adm-input::placeholder{color:#2a2a2a}
.adm-select{background:#0a0a0a;border:1px solid #2a2a2a;color:var(--white);font-family:'DM Mono',monospace;font-size:0.8rem;padding:0.55rem 0.75rem;border-radius:4px;outline:none;width:100%;cursor:pointer}
.adm-select option{background:#0a0a0a}
.btn-primary{background:var(--white);color:var(--black);border:none;font-family:'Bebas Neue',sans-serif;font-size:1.05rem;letter-spacing:0.25em;padding:0.7rem 1.4rem;border-radius:4px;cursor:pointer;transition:opacity 0.2s}
.btn-primary:hover{opacity:0.85}
.btn-primary.purple{background:var(--purple);color:#1a0030}
.btn-ghost{background:transparent;border:1px solid #2a2a2a;color:#555;font-family:'DM Mono',monospace;font-size:0.6rem;letter-spacing:0.1em;padding:0.5rem 0.8rem;border-radius:4px;cursor:pointer;transition:all 0.2s;text-transform:uppercase}
.btn-ghost:hover{border-color:#555;color:#aaa}
.btn-ghost.danger:hover{border-color:var(--red);color:var(--red)}
.btn-ghost.warn:hover{border-color:var(--yellow);color:var(--yellow)}
.btn-ghost.success:hover{border-color:var(--green);color:var(--green)}
.btn-ghost.purple-h:hover{border-color:var(--purple);color:var(--purple)}
.form-fb{font-size:0.6rem;min-height:1rem;letter-spacing:0.1em;color:var(--green)}
.form-fb.err{color:var(--red)}

/* ══ TABLE ══ */
.tbl-wrap{background:#111;border:1px solid #1e1e1e;border-radius:6px;overflow:hidden}
.tbl-head{display:grid;gap:1px;background:#1a1a1a;padding:0.5rem 0.8rem;align-items:center}
.th{font-size:0.5rem;letter-spacing:0.18em;text-transform:uppercase;color:#2a2a2a}
.tbl-body{display:flex;flex-direction:column;gap:1px;background:#1a1a1a;max-height:400px;overflow-y:auto;scrollbar-width:thin;scrollbar-color:#222 transparent}
.tbl-body::-webkit-scrollbar{width:3px}
.tbl-body::-webkit-scrollbar-thumb{background:#222}
.tbl-row{display:grid;gap:1px;background:#0d0d0d;padding:0.6rem 0.8rem;align-items:center;transition:background 0.15s;cursor:pointer}
.tbl-row:hover{background:#161616}
.tbl-row.banned-row{opacity:0.45}
.tbl-row.selected-row{background:#1a1a1a;border-left:2px solid #555}
.td{font-size:0.72rem;overflow:hidden;text-overflow:ellipsis;white-space:nowrap}
.td.name{color:var(--white)}
.td.role-admin{color:var(--yellow);font-size:0.62rem}
.td.role-master{color:var(--purple);font-size:0.62rem}
.td.role-user{color:#444;font-size:0.62rem}
.td.bal{font-family:'Bebas Neue',sans-serif;letter-spacing:0.05em}
.td.status-active{color:var(--green);font-size:0.6rem}
.td.status-banned{color:var(--red);font-size:0.6rem}
.td.status-restricted{color:var(--yellow);font-size:0.6rem}
.td.num{color:#555;font-size:0.65rem}
.td.actions{display:flex;gap:3px;flex-wrap:wrap}
.act-btn{background:transparent;border:1px solid #1e1e1e;color:#444;font-size:0.5rem;padding:0.18rem 0.32rem;border-radius:3px;cursor:pointer;transition:all 0.15s;font-family:'DM Mono',monospace}
.act-btn:hover{border-color:#555;color:#aaa}

/* ══ DETAIL PANEL ══ */
.detail-panel{background:#111;border:1px solid #1e1e1e;border-radius:6px;padding:1.2rem;display:none;flex-direction:column;gap:1rem}
.detail-panel.open{display:flex}
.dp-header{display:flex;align-items:flex-start;justify-content:space-between}
.dp-name{font-family:'Bebas Neue',sans-serif;font-size:1.7rem;letter-spacing:0.1em}
.dp-meta{font-size:0.57rem;color:#444;letter-spacing:0.1em;margin-top:0.2rem;line-height:1.7}
.dp-close{background:transparent;border:none;color:#333;font-size:1.1rem;cursor:pointer;transition:color 0.2s}
.dp-close:hover{color:#888}
.dp-grid{display:flex;flex-direction:column;gap:0.7rem}
.dp-row{display:flex;align-items:center;gap:0.8rem;flex-wrap:wrap;padding:0.5rem 0;border-bottom:1px solid #161616}
.dp-row:last-child{border-bottom:none}
.dp-label{font-size:0.55rem;letter-spacing:0.15em;text-transform:uppercase;color:#333;min-width:110px}
.dp-inp{background:#0a0a0a;border:1px solid #222;color:var(--white);font-family:'DM Mono',monospace;font-size:0.78rem;padding:0.4rem 0.65rem;border-radius:4px;outline:none;width:110px;transition:border-color 0.2s}
.dp-inp:focus{border-color:#555}
.toggle-wrap{display:flex;align-items:center;gap:0.6rem}
.toggle{width:36px;height:20px;background:#1a1a1a;border:1px solid #2a2a2a;border-radius:10px;position:relative;cursor:pointer;transition:all 0.2s;flex-shrink:0}
.toggle.on{background:#2a2a2a;border-color:#444}
.toggle::after{content:'';position:absolute;width:13px;height:13px;background:#333;border-radius:50%;top:2.5px;left:2.5px;transition:transform 0.2s,background 0.2s}
.toggle.on::after{transform:translateX(16px);background:var(--white)}
.toggle-label{font-size:0.6rem;color:#444;letter-spacing:0.08em}
.dp-fb{font-size:0.6rem;color:var(--green);min-height:1rem;letter-spacing:0.1em}
.dp-fb.err{color:var(--red)}

/* ══ TABS ══ */
.tab-bar{display:flex;gap:1px;background:#1a1a1a;border-radius:4px;overflow:hidden;border:1px solid #1a1a1a}
.tab-btn{flex:1;padding:0.6rem;background:#111;border:none;color:#444;font-family:'DM Mono',monospace;font-size:0.6rem;letter-spacing:0.15em;text-transform:uppercase;cursor:pointer;transition:all 0.2s}
.tab-btn.active{background:var(--white);color:var(--black)}
.tab-btn.active.purple-tab{background:var(--purple);color:#1a0030}
.tab-pane{display:none;flex-direction:column;gap:1.4rem}
.tab-pane.active{display:flex}

/* ══ GAME SCREEN ══ */
.game-page{position:relative;z-index:1;width:100%;max-width:420px;display:flex;flex-direction:column;align-items:center;gap:1.4rem;margin:0 auto}
.wallet-card{width:100%;background:#111;border:1px solid #1e1e1e;border-radius:8px;padding:1.2rem 1.4rem;position:relative;overflow:hidden}
.wallet-card::before{content:'WALLET';position:absolute;top:1rem;right:1.2rem;font-family:'Bebas Neue',sans-serif;font-size:0.9rem;letter-spacing:0.3em;color:#1e1e1e}
.wallet-top{display:flex;align-items:flex-end;gap:0.4rem}
.wallet-label{font-size:0.6rem;letter-spacing:0.2em;text-transform:uppercase;color:var(--mid);display:block;margin-bottom:0.3rem}
.wallet-amount{font-family:'Bebas Neue',sans-serif;font-size:clamp(2.4rem,10vw,3.2rem);letter-spacing:0.05em;line-height:1;transition:color 0.3s}
.wallet-amount.win-flash{color:var(--green)}.wallet-amount.lose-flash{color:var(--red)}
.wallet-currency{font-size:1rem;color:var(--mid);margin-bottom:0.4rem}
.wallet-delta{font-size:0.75rem;font-weight:500;opacity:0;transform:translateY(4px);transition:opacity 0.3s,transform 0.3s;min-height:1.2rem;margin-top:0.2rem}
.wallet-delta.show{opacity:1;transform:translateY(0)}
.wallet-delta.pos{color:var(--green)}.wallet-delta.neg{color:var(--red)}
.wallet-restricted{font-size:0.58rem;letter-spacing:0.12em;text-transform:uppercase;color:var(--red);margin-top:0.4rem;display:none}
.wallet-restricted.show{display:block}
.wallet-notice{font-size:0.58rem;letter-spacing:0.12em;color:var(--yellow);margin-top:0.3rem;display:none}
.wallet-notice.show{display:block}
.wallet-stats{display:flex;gap:1px;margin-top:1rem;background:#1a1a1a;border-radius:4px;overflow:hidden}
.wstat{flex:1;background:#0d0d0d;padding:0.5rem 0.4rem;text-align:center}
.wstat-label{font-size:0.48rem;letter-spacing:0.15em;text-transform:uppercase;color:#2a2a2a;display:block}
.wstat-val{font-size:0.9rem;font-weight:500;display:block;line-height:1.3}
.wstat-val.g{color:var(--green)}.wstat-val.r{color:var(--red)}.wstat-val.n{color:#444}
.bet-section{width:100%}
.section-label{font-size:0.58rem;letter-spacing:0.22em;text-transform:uppercase;color:var(--mid);display:block;margin-bottom:0.6rem}
.bet-chips{display:flex;gap:0.5rem;flex-wrap:wrap}
.chip{flex:1;min-width:46px;padding:0.5rem 0.3rem;border:1px solid #2a2a2a;background:transparent;color:#555;font-family:'DM Mono',monospace;font-size:0.7rem;font-weight:500;cursor:pointer;border-radius:4px;transition:all 0.18s;text-align:center}
.chip:hover{border-color:#555;color:#aaa}
.chip.active{background:var(--white);color:var(--black);border-color:var(--white)}
.chip.chip-disabled{opacity:0.2;cursor:not-allowed;pointer-events:none}
.bet-custom{display:flex;gap:0.5rem;margin-top:0.5rem;align-items:center}
.bet-input{flex:1;background:#0d0d0d;border:1px solid #2a2a2a;color:var(--white);font-family:'DM Mono',monospace;font-size:0.85rem;padding:0.55rem 0.8rem;border-radius:4px;outline:none;transition:border-color 0.2s}
.bet-input:focus{border-color:#555}
.bet-input::placeholder{color:#2a2a2a}
.all-in-btn{background:transparent;border:1px solid #2a2a2a;color:#444;font-family:'DM Mono',monospace;font-size:0.58rem;letter-spacing:0.1em;padding:0.55rem 0.7rem;border-radius:4px;cursor:pointer;text-transform:uppercase;transition:all 0.2s;white-space:nowrap}
.all-in-btn:hover{border-color:#555;color:#aaa}
.coin-scene{perspective:800px;width:170px;height:170px}
.coin{width:100%;height:100%;position:relative;transform-style:preserve-3d}
.coin.flipping{animation:flip 1s cubic-bezier(0.4,0,0.2,1) forwards}
.coin.heads-result{--final:rotateY(0deg)}.coin.tails-result{--final:rotateY(180deg)}
@keyframes flip{0%{transform:rotateY(0deg) translateY(0)}20%{transform:rotateY(360deg) translateY(-28px)}50%{transform:rotateY(900deg) translateY(-55px)}80%{transform:rotateY(1440deg) translateY(-18px)}100%{transform:var(--final,rotateY(0deg)) translateY(0)}}
.coin-face{position:absolute;inset:0;border-radius:50%;backface-visibility:hidden;-webkit-backface-visibility:hidden;display:flex;align-items:center;justify-content:center;flex-direction:column;gap:0.2rem;border:3px solid #2a2a2a}
.coin-face.heads{background:radial-gradient(circle at 35% 35%,#f0f0e8,#b0b0a8);box-shadow:inset 0 0 30px rgba(0,0,0,0.3),0 0 0 6px #111,0 0 0 9px #222,0 8px 40px rgba(0,0,0,0.8)}
.coin-face.tails{background:radial-gradient(circle at 65% 65%,#4a4a4a,#1a1a1a);box-shadow:inset 0 0 30px rgba(255,255,255,0.05),0 0 0 6px #111,0 0 0 9px #222,0 8px 40px rgba(0,0,0,0.8);transform:rotateY(180deg)}
.coin-symbol{font-family:'Bebas Neue',sans-serif;font-size:2.8rem;line-height:1}
.coin-face.heads .coin-symbol{color:rgba(0,0,0,0.35)}.coin-face.tails .coin-symbol{color:rgba(255,255,255,0.15)}
.coin-label{font-size:0.5rem;letter-spacing:0.3em;text-transform:uppercase;font-family:'DM Mono',monospace}
.coin-face.heads .coin-label{color:rgba(0,0,0,0.4)}.coin-face.tails .coin-label{color:rgba(255,255,255,0.2)}
.result-display{height:1.8rem;display:flex;align-items:center;justify-content:center}
.result-text{font-family:'Bebas Neue',sans-serif;font-size:1.8rem;letter-spacing:0.2em;opacity:0;transform:translateY(8px);transition:opacity 0.4s,transform 0.4s}
.result-text.visible{opacity:1;transform:translateY(0)}
.result-text.heads-r{color:var(--white)}.result-text.tails-r{color:var(--mid)}
.pick-label{font-size:0.58rem;letter-spacing:0.22em;text-transform:uppercase;color:var(--mid);text-align:center}
.pick-buttons{display:flex;gap:1rem;width:100%}
.pick-btn{flex:1;padding:0.95rem;border:1px solid #2a2a2a;background:transparent;color:var(--mid);font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:0.15em;cursor:pointer;border-radius:4px;transition:all 0.2s;position:relative;overflow:hidden}
.pick-btn::before{content:'';position:absolute;inset:0;background:var(--white);transform:scaleX(0);transform-origin:left;transition:transform 0.2s;z-index:0}
.pick-btn span{position:relative;z-index:1}
.pick-btn:hover{color:var(--black);border-color:var(--white)}.pick-btn:hover::before{transform:scaleX(1)}
.pick-btn.selected{background:var(--white);color:var(--black);border-color:var(--white)}.pick-btn.selected::before{transform:scaleX(1)}
.pick-btn.wrong{background:transparent;color:#333;border-color:#2a2a2a;animation:shake 0.4s}
.pick-btn.correct{background:var(--white);color:var(--black);border-color:var(--white)}
@keyframes shake{0%,100%{transform:translateX(0)}25%{transform:translateX(-4px)}75%{transform:translateX(4px)}}
.flip-btn{width:100%;padding:1.1rem;background:var(--white);color:var(--black);border:none;font-family:'Bebas Neue',sans-serif;font-size:1.4rem;letter-spacing:0.3em;cursor:pointer;border-radius:4px;transition:all 0.2s;position:relative;overflow:hidden}
.flip-btn::after{content:'';position:absolute;inset:0;background:rgba(0,0,0,0.15);opacity:0;transition:opacity 0.2s}
.flip-btn:hover::after{opacity:1}.flip-btn:active{transform:scale(0.98)}
.flip-btn:disabled{background:#141414;color:#2a2a2a;cursor:not-allowed}.flip-btn:disabled::after{display:none}
.tx-section{width:100%}
.tx-log{display:flex;flex-direction:column;gap:1px;max-height:145px;overflow-y:auto;scrollbar-width:thin;scrollbar-color:#1e1e1e transparent}
.tx-log::-webkit-scrollbar{width:3px}.tx-log::-webkit-scrollbar-thumb{background:#1e1e1e}
.tx-empty{font-size:0.58rem;color:#2a2a2a;padding:0.4rem 0.7rem}
.tx-row{display:flex;align-items:center;gap:0.6rem;padding:0.42rem 0.7rem;background:#0d0d0d;border-radius:3px;font-size:0.63rem;opacity:0;transform:translateY(-5px);transition:opacity 0.3s,transform 0.3s}
.tx-row.show{opacity:1;transform:translateY(0)}
.tx-face{width:17px;height:17px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-family:'Bebas Neue',sans-serif;font-size:0.65rem;flex-shrink:0}
.tx-face.h{background:radial-gradient(circle at 35% 35%,#d0d0c8,#888);color:rgba(0,0,0,0.5)}
.tx-face.t{background:radial-gradient(circle at 65% 65%,#444,#1a1a1a);color:rgba(255,255,255,0.3);border:1px solid #333}
.tx-info{flex:1;color:#444}.tx-info strong{color:#777}
.tx-amount{font-weight:500;letter-spacing:0.05em;flex-shrink:0}
.tx-amount.win{color:var(--green)}.tx-amount.lose{color:var(--red)}
.tx-time{color:#222;font-size:0.52rem;flex-shrink:0}
.history-wrap{width:100%}
.history-label{font-size:0.55rem;letter-spacing:0.22em;text-transform:uppercase;color:#2a2a2a;display:block;margin-bottom:0.5rem}
.history-dots{display:flex;gap:5px;flex-wrap:wrap}
.h-dot{width:8px;height:8px;border-radius:50%;opacity:0;transform:scale(0);transition:all 0.3s}
.h-dot.show{opacity:1;transform:scale(1)}
.h-dot.h{background:var(--white)}.h-dot.t{background:#2a2a2a;border:1px solid #444}
.h-dot.win{box-shadow:0 0 0 2px #444}

/* ══ ANNOUNCEMENT BANNER ══ */
.announcement-banner{width:100%;background:#161616;border:1px solid #222;border-left:3px solid var(--purple);border-radius:4px;padding:0.7rem 1rem;display:none;align-items:center;gap:0.8rem}
.announcement-banner.show{display:flex}
.ann-icon{font-size:0.9rem;flex-shrink:0}
.ann-text{font-size:0.65rem;color:#aaa;letter-spacing:0.06em;flex:1}
.ann-close{background:transparent;border:none;color:#333;cursor:pointer;font-size:0.9rem;transition:color 0.2s}
.ann-close:hover{color:#888}

/* ══ GLOBAL SETTINGS DISPLAY ══ */
.global-notice{width:100%;background:#111;border:1px solid #1e1e1e;border-radius:4px;padding:0.6rem 0.9rem;font-size:0.6rem;color:#444;display:flex;align-items:center;gap:0.6rem}
.global-notice span{color:#555}

/* ══ ACTIVITY LOG ══ */
.activity-log{display:flex;flex-direction:column;gap:1px;max-height:300px;overflow-y:auto;scrollbar-width:thin;scrollbar-color:#1e1e1e transparent}
.activity-log::-webkit-scrollbar{width:3px}.activity-log::-webkit-scrollbar-thumb{background:#1e1e1e}
.log-row{display:flex;align-items:flex-start;gap:0.7rem;padding:0.5rem 0.7rem;background:#0d0d0d;border-radius:3px;font-size:0.62rem}
.log-time{color:#2a2a2a;flex-shrink:0;font-size:0.55rem;padding-top:0.05rem}
.log-icon{flex-shrink:0;font-size:0.8rem}
.log-msg{color:#444;flex:1;line-height:1.5}
.log-msg strong{color:#666}

/* ══ BROKE OVERLAY ══ */
.broke-overlay{position:fixed;inset:0;background:rgba(0,0,0,0.94);z-index:200;display:flex;align-items:center;justify-content:center;opacity:0;pointer-events:none;transition:opacity 0.4s}
.broke-overlay.show{opacity:1;pointer-events:all}
.broke-box{text-align:center;padding:2rem}
.broke-box h2{font-family:'Bebas Neue',sans-serif;font-size:3.5rem;letter-spacing:0.1em;color:var(--red)}
.broke-box p{font-size:0.7rem;letter-spacing:0.15em;color:var(--mid);margin:0.6rem 0 1.8rem;text-transform:uppercase;line-height:1.7}
.broke-close{background:var(--white);color:var(--black);border:none;font-family:'Bebas Neue',sans-serif;font-size:1.3rem;letter-spacing:0.3em;padding:1rem 2.5rem;border-radius:4px;cursor:pointer}

/* search */
.srch{background:#0a0a0a;border:1px solid #1e1e1e;color:var(--white);font-family:'DM Mono',monospace;font-size:0.75rem;padding:0.5rem 0.8rem;border-radius:4px;outline:none;transition:border-color 0.2s}
.srch:focus{border-color:#555}.srch::placeholder{color:#2a2a2a}

footer{font-size:0.48rem;letter-spacing:0.2em;color:#1e1e1e;text-transform:uppercase;text-align:center;padding:1.5rem 0 0.5rem;position:relative;z-index:1}
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="topbar" id="topbar">
  <div class="topbar-logo" onclick="showGame()">COIN<span>FLIP</span></div>
  <div class="topbar-right">
    <span class="topbar-user" id="topbarUser"></span>
    <span class="role-badge" id="roleBadge">user</span>
    <button class="nav-btn" id="btnNavAdmin" onclick="showAdmin()" style="display:none">⚙ Admin</button>
    <button class="nav-btn" id="btnNavMaster" onclick="showMaster()" style="display:none">◆ Master</button>
    <button class="nav-btn" onclick="logout()">Sign Out</button>
  </div>
</div>

<!-- ═══════════ AUTH ═══════════ -->
<div class="screen active" id="authScreen">
  <div class="auth-box">
    <div class="auth-logo">
      <h1>COIN<span>FLIP</span></h1>
      <p>The House Always Wins. Or Does It?</p>
    </div>
    <div class="auth-tabs">
      <button class="auth-tab active" id="tabSI" onclick="switchTab('signin')">Sign In</button>
      <button class="auth-tab" id="tabSU" onclick="switchTab('signup')">Sign Up</button>
    </div>
    <div id="formSI" class="auth-form">
      <div><label class="field-label">Username</label><input class="field-input" id="siU" type="text" placeholder="Username" autocomplete="username"/></div>
      <div><label class="field-label">Password</label><input class="field-input" id="siP" type="password" placeholder="Password" autocomplete="current-password"/></div>
      <div class="auth-err" id="siErr"></div>
      <button class="auth-btn" onclick="doSignIn()">Sign In</button>
    </div>
    <div id="formSU" class="auth-form" style="display:none">
      <div><label class="field-label">Username</label><input class="field-input" id="suU" type="text" placeholder="Choose username" autocomplete="username"/></div>
      <div><label class="field-label">Password</label><input class="field-input" id="suP" type="password" placeholder="Min 6 chars" autocomplete="new-password"/></div>
      <div><label class="field-label">Confirm Password</label><input class="field-input" id="suP2" type="password" placeholder="Repeat password" autocomplete="new-password"/></div>
      <div class="auth-err" id="suErr"></div>
      <button class="auth-btn" onclick="doSignUp()">Create Account</button>
    </div>
    <div class="auth-divider"><span>demo credentials</span></div>
    <div class="demo-creds">
      master: <strong>master</strong> / <strong>master123</strong><br>
      admin: <strong>admin</strong> / <strong>admin123</strong>
    </div>
  </div>
</div>

<!-- ═══════════ GAME ═══════════ -->
<div class="screen" id="gameScreen">
  <div class="game-page">
    <div class="announcement-banner" id="annBanner">
      <span class="ann-icon">📢</span>
      <span class="ann-text" id="annText"></span>
      <button class="ann-close" onclick="dismissAnnouncement()">✕</button>
    </div>
    <div class="wallet-card">
      <span class="wallet-label">Balance</span>
      <div class="wallet-top">
        <span class="wallet-currency">$</span>
        <span class="wallet-amount" id="walletAmt">0</span>
      </div>
      <div class="wallet-delta" id="walletDelta"></div>
      <div class="wallet-restricted" id="walletRestricted">⛔ Wallet locked by admin — betting disabled</div>
      <div class="wallet-notice" id="walletNotice"></div>
      <div class="wallet-stats">
        <div class="wstat"><span class="wstat-label">Won</span><span class="wstat-val g" id="totalWon">$0</span></div>
        <div class="wstat"><span class="wstat-label">Lost</span><span class="wstat-val r" id="totalLost">$0</span></div>
        <div class="wstat"><span class="wstat-label">P&amp;L</span><span class="wstat-val n" id="totalPnl">$0</span></div>
        <div class="wstat"><span class="wstat-label">Win%</span><span class="wstat-val n" id="winRate">—</span></div>
      </div>
    </div>
    <div class="bet-section" id="betSection">
      <span class="section-label">Place your bet</span>
      <div class="bet-chips">
        <button class="chip" data-amt="10" onclick="selectChip(10,this)">$10</button>
        <button class="chip" data-amt="25" onclick="selectChip(25,this)">$25</button>
        <button class="chip" data-amt="50" onclick="selectChip(50,this)">$50</button>
        <button class="chip" data-amt="100" onclick="selectChip(100,this)">$100</button>
        <button class="chip" data-amt="250" onclick="selectChip(250,this)">$250</button>
      </div>
      <div class="bet-custom">
        <input class="bet-input" type="number" id="betInput" placeholder="Custom amount…" min="1" oninput="onBetInput()"/>
        <button class="all-in-btn" onclick="allIn()">All In</button>
      </div>
    </div>
    <div class="coin-scene">
      <div class="coin" id="coin">
        <div class="coin-face heads"><div class="coin-symbol">H</div><div class="coin-label">Heads</div></div>
        <div class="coin-face tails"><div class="coin-symbol">T</div><div class="coin-label">Tails</div></div>
      </div>
    </div>
    <div class="result-display"><div class="result-text" id="resultText"></div></div>
    <div class="pick-label" id="pickLabel">— Choose your side —</div>
    <div class="pick-buttons">
      <button class="pick-btn" id="btnHeads" onclick="pick('heads')"><span>Heads</span></button>
      <button class="pick-btn" id="btnTails" onclick="pick('tails')"><span>Tails</span></button>
    </div>
    <button class="flip-btn" id="flipBtn" onclick="flipCoin()" disabled>FLIP THE COIN</button>
    <div class="tx-section">
      <span class="section-label">Transactions</span>
      <div class="tx-log" id="txLog"><div class="tx-empty">No bets yet</div></div>
    </div>
    <div class="history-wrap">
      <span class="history-label">History</span>
      <div class="history-dots" id="historyDots"></div>
    </div>
    <footer>© 2026 coinflip · 1:1 payout</footer>
  </div>
</div>

<!-- ═══════════ ADMIN ═══════════ -->
<div class="screen" id="adminScreen">
  <div class="panel-wrap">
    <div class="panel-header">
      <div>
        <div class="panel-title">ADMIN <span>PANEL</span></div>
        <div class="panel-subtitle">User management &amp; wallet controls</div>
      </div>
      <div class="panel-actions">
        <span class="role-badge admin">ADMIN</span>
        <button class="nav-btn active-nav" onclick="showGame()">← Game</button>
      </div>
    </div>
    <div class="stat-grid" id="adminStats"></div>
    <div class="tab-bar">
      <button class="tab-btn active" onclick="switchAdminTab('users',this)">Users</button>
      <button class="tab-btn" onclick="switchAdminTab('create',this)">Create User</button>
      <button class="tab-btn" onclick="switchAdminTab('activity',this)">Activity</button>
    </div>

    <!-- Tab: Users -->
    <div class="tab-pane active" id="adminTab-users">
      <div class="detail-panel" id="adminDetailPanel">
        <div class="dp-header">
          <div>
            <div class="dp-name" id="adpName"></div>
            <div class="dp-meta" id="adpMeta"></div>
          </div>
          <button class="dp-close" onclick="closeAdminDetail()">✕</button>
        </div>
        <div class="dp-grid">
          <div class="dp-row"><span class="dp-label">Set Balance</span><input class="dp-inp" type="number" id="adpBal" placeholder="Amount"/><button class="btn-ghost success" onclick="adminSetBal()">Apply</button></div>
          <div class="dp-row"><span class="dp-label">Add / Deduct</span><input class="dp-inp" type="number" id="adpAdd" placeholder="Amount"/><button class="btn-ghost success" onclick="adminAddFunds()">Add</button><button class="btn-ghost danger" onclick="adminDeductFunds()">Deduct</button></div>
          <div class="dp-row"><span class="dp-label">Max Bet Cap</span><input class="dp-inp" type="number" id="adpMaxBet" placeholder="0 = unlimited"/><button class="btn-ghost success" onclick="adminSetMaxBet()">Apply</button></div>
          <div class="dp-row"><span class="dp-label">Wallet Lock</span><div class="toggle-wrap"><div class="toggle" id="adpWLock" onclick="adminToggleWalletLock()"></div><span class="toggle-label" id="adpWLockLbl">Unlocked</span></div></div>
          <div class="dp-row"><span class="dp-label">Account</span><button class="btn-ghost warn" id="adpBanBtn" onclick="adminToggleBan()">Ban</button><button class="btn-ghost danger" onclick="adminDeleteUser()">Delete</button></div>
        </div>
        <div class="dp-fb" id="adpFb"></div>
      </div>
      <div style="display:flex;align-items:center;justify-content:space-between;gap:0.8rem;flex-wrap:wrap">
        <span class="section-title" style="flex:1">All Users</span>
        <input class="srch" id="adminSearch" placeholder="Search…" oninput="renderAdminUsers()" style="max-width:180px"/>
      </div>
      <div class="tbl-wrap">
        <div class="tbl-head" style="grid-template-columns:1fr 70px 90px 80px 80px 80px">
          <span class="th">User</span><span class="th">Role</span><span class="th">Balance</span><span class="th">Status</span><span class="th">Max Bet</span><span class="th">Actions</span>
        </div>
        <div class="tbl-body" id="adminUsersList"></div>
      </div>
    </div>

    <!-- Tab: Create -->
    <div class="tab-pane" id="adminTab-create">
      <div class="form-card">
        <div class="form-row">
          <div class="fw"><label class="field-label">Username</label><input class="adm-input" id="cuUser" placeholder="e.g. john_doe"/></div>
          <div class="fw"><label class="field-label">Password</label><input class="adm-input" type="password" id="cuPass" placeholder="Min 6 chars"/></div>
        </div>
        <div class="form-row">
          <div class="fw"><label class="field-label">Role</label>
            <select class="adm-select" id="cuRole"><option value="user">User</option><option value="admin">Admin</option></select>
          </div>
          <div class="fw"><label class="field-label">Starting Balance ($)</label><input class="adm-input" type="number" id="cuBal" value="1000"/></div>
        </div>
        <div style="display:flex;align-items:center;gap:1rem;flex-wrap:wrap">
          <button class="btn-primary" onclick="adminCreateUser()">Create User</button>
          <div class="form-fb" id="cuFb"></div>
        </div>
      </div>
    </div>

    <!-- Tab: Activity -->
    <div class="tab-pane" id="adminTab-activity">
      <div class="activity-log" id="adminActivityLog"><div style="font-size:0.6rem;color:#2a2a2a;padding:0.5rem">No activity yet</div></div>
    </div>
  </div>
</div>

<!-- ═══════════ MASTER ═══════════ -->
<div class="screen" id="masterScreen">
  <div class="panel-wrap">
    <div class="panel-header">
      <div>
        <div class="panel-title">MASTER <span class="purple">PANEL</span></div>
        <div class="panel-subtitle">Full platform sovereignty · God-mode access</div>
      </div>
      <div class="panel-actions">
        <span class="role-badge master">MASTER</span>
        <button class="nav-btn active-nav" onclick="showGame()">← Game</button>
      </div>
    </div>
    <div class="stat-grid" id="masterStats"></div>

    <div class="tab-bar">
      <button class="tab-btn active purple-tab" onclick="switchMasterTab('overview',this)">Overview</button>
      <button class="tab-btn" onclick="switchMasterTab('users',this)">All Users</button>
      <button class="tab-btn" onclick="switchMasterTab('admins',this)">Admins</button>
      <button class="tab-btn" onclick="switchMasterTab('settings',this)">Settings</button>
      <button class="tab-btn" onclick="switchMasterTab('broadcast',this)">Broadcast</button>
      <button class="tab-btn" onclick="switchMasterTab('logs',this)">Logs</button>
    </div>

    <!-- Tab: Overview -->
    <div class="tab-pane active" id="masterTab-overview">
      <div class="section-block">
        <span class="section-title">Platform Health</span>
        <div class="stat-grid" id="masterHealthGrid"></div>
      </div>
      <div class="section-block">
        <span class="section-title">Top Players by Balance</span>
        <div class="tbl-wrap">
          <div class="tbl-head" style="grid-template-columns:30px 1fr 100px 80px 80px">
            <span class="th">#</span><span class="th">User</span><span class="th">Balance</span><span class="th">Wins</span><span class="th">Flips</span>
          </div>
          <div class="tbl-body" id="masterLeaderboard"></div>
        </div>
      </div>
      <div class="section-block">
        <span class="section-title">Recent Activity</span>
        <div class="activity-log" id="masterRecentLog"><div style="font-size:0.6rem;color:#2a2a2a;padding:0.5rem">No activity yet</div></div>
      </div>
    </div>

    <!-- Tab: All Users (master view) -->
    <div class="tab-pane" id="masterTab-users">
      <div class="detail-panel" id="masterDetailPanel">
        <div class="dp-header">
          <div>
            <div class="dp-name" id="mdpName"></div>
            <div class="dp-meta" id="mdpMeta"></div>
          </div>
          <button class="dp-close" onclick="closeMasterDetail()">✕</button>
        </div>
        <div class="dp-grid">
          <div class="dp-row"><span class="dp-label">Set Balance</span><input class="dp-inp" type="number" id="mdpBal"/><button class="btn-ghost success" onclick="masterSetBal()">Apply</button></div>
          <div class="dp-row"><span class="dp-label">Add / Deduct</span><input class="dp-inp" type="number" id="mdpAdd" placeholder="Amount"/><button class="btn-ghost success" onclick="masterAddFunds()">Add</button><button class="btn-ghost danger" onclick="masterDeductFunds()">Deduct</button></div>
          <div class="dp-row"><span class="dp-label">Max Bet Cap</span><input class="dp-inp" type="number" id="mdpMaxBet" placeholder="0 = unlimited"/><button class="btn-ghost success" onclick="masterSetMaxBet()">Apply</button></div>
          <div class="dp-row"><span class="dp-label">Change Role</span>
            <select class="adm-select" id="mdpRole" style="width:130px"><option value="user">User</option><option value="admin">Admin</option><option value="master">Master</option></select>
            <button class="btn-ghost purple-h" onclick="masterSetRole()">Apply</button>
          </div>
          <div class="dp-row"><span class="dp-label">Wallet Lock</span><div class="toggle-wrap"><div class="toggle" id="mdpWLock" onclick="masterToggleWalletLock()"></div><span class="toggle-label" id="mdpWLockLbl">Unlocked</span></div></div>
          <div class="dp-row"><span class="dp-label">Game Access</span><div class="toggle-wrap"><div class="toggle" id="mdpGameLock" onclick="masterToggleGameLock()"></div><span class="toggle-label" id="mdpGameLockLbl">Allowed</span></div></div>
          <div class="dp-row"><span class="dp-label">Account</span><button class="btn-ghost warn" id="mdpBanBtn" onclick="masterToggleBan()">Ban</button><button class="btn-ghost danger" onclick="masterResetStats()">Reset Stats</button><button class="btn-ghost danger" onclick="masterDeleteUser()">Delete</button></div>
        </div>
        <div class="dp-fb" id="mdpFb"></div>
      </div>
      <div style="display:flex;align-items:center;justify-content:space-between;gap:0.8rem;flex-wrap:wrap">
        <span class="section-title" style="flex:1">All Users</span>
        <input class="srch" id="masterSearch" placeholder="Search…" oninput="renderMasterUsers()" style="max-width:180px"/>
      </div>
      <div class="tbl-wrap">
        <div class="tbl-head" style="grid-template-columns:1fr 70px 90px 80px 70px 80px">
          <span class="th">User</span><span class="th">Role</span><span class="th">Balance</span><span class="th">Status</span><span class="th">Flips</span><span class="th">Actions</span>
        </div>
        <div class="tbl-body" id="masterUsersList"></div>
      </div>
    </div>

    <!-- Tab: Admins -->
    <div class="tab-pane" id="masterTab-admins">
      <div class="section-block">
        <span class="section-title">Create Admin</span>
        <div class="form-card">
          <div class="form-row">
            <div class="fw"><label class="field-label">Username</label><input class="adm-input" id="maUser" placeholder="Admin username"/></div>
            <div class="fw"><label class="field-label">Password</label><input class="adm-input" type="password" id="maPass" placeholder="Min 6 chars"/></div>
            <div class="fw"><label class="field-label">Balance ($)</label><input class="adm-input" type="number" id="maBal" value="5000"/></div>
          </div>
          <div style="display:flex;align-items:center;gap:1rem">
            <button class="btn-primary purple" onclick="masterCreateAdmin()">Create Admin</button>
            <div class="form-fb" id="maFb"></div>
          </div>
        </div>
      </div>
      <div class="section-block">
        <span class="section-title">Admin Accounts</span>
        <div class="tbl-wrap">
          <div class="tbl-head" style="grid-template-columns:1fr 90px 80px 80px 80px">
            <span class="th">Username</span><span class="th">Balance</span><span class="th">Status</span><span class="th">Flips</span><span class="th">Actions</span>
          </div>
          <div class="tbl-body" id="masterAdminsList"></div>
        </div>
      </div>
    </div>

    <!-- Tab: Settings -->
    <div class="tab-pane" id="masterTab-settings">
      <div class="section-block">
        <span class="section-title">Global Game Settings</span>
        <div class="form-card">
          <div class="form-row">
            <div class="fw"><label class="field-label">Global Max Bet ($)</label><input class="adm-input" type="number" id="gsMaxBet" placeholder="0 = unlimited"/></div>
            <div class="fw"><label class="field-label">Starting Balance ($)</label><input class="adm-input" type="number" id="gsStartBal" placeholder="Default: 1000"/></div>
          </div>
          <div class="dp-row" style="border:none;padding:0">
            <span class="dp-label" style="min-width:140px">Maintenance Mode</span>
            <div class="toggle-wrap">
              <div class="toggle" id="gsMaintToggle" onclick="toggleMaintenance()"></div>
              <span class="toggle-label" id="gsMaintLbl">Off</span>
            </div>
          </div>
          <div class="dp-row" style="border:none;padding:0">
            <span class="dp-label" style="min-width:140px">New Signups</span>
            <div class="toggle-wrap">
              <div class="toggle on" id="gsSignupToggle" onclick="toggleSignups()"></div>
              <span class="toggle-label" id="gsSignupLbl">Allowed</span>
            </div>
          </div>
          <div style="display:flex;gap:0.8rem;flex-wrap:wrap;align-items:center">
            <button class="btn-primary purple" onclick="applyGlobalSettings()">Apply Settings</button>
            <div class="form-fb" id="gsFb"></div>
          </div>
        </div>
      </div>
      <div class="section-block">
        <span class="section-title">Bulk Actions</span>
        <div class="form-card">
          <div class="form-row">
            <div class="fw"><label class="field-label">Refill All Users ($)</label><input class="adm-input" type="number" id="bulkRefill" placeholder="Amount per user"/></div>
            <div class="fw" style="justify-content:flex-end;padding-top:1.4rem"><button class="btn-primary" onclick="masterBulkRefill()">Refill All</button></div>
          </div>
          <div class="form-row">
            <div class="fw"><label class="field-label">Lock All Wallets</label></div>
            <div class="fw" style="padding-top:1.4rem;display:flex;gap:0.5rem">
              <button class="btn-ghost danger" onclick="masterLockAll()">Lock All</button>
              <button class="btn-ghost success" onclick="masterUnlockAll()">Unlock All</button>
            </div>
          </div>
          <div class="form-fb" id="bulkFb"></div>
        </div>
      </div>
      <div class="section-block">
        <span class="section-title">Danger Zone</span>
        <div class="form-card" style="border-color:#2a1a1a">
          <div class="form-row">
            <div class="fw"><label class="field-label" style="color:var(--red)">Reset All User Stats</label><div style="font-size:0.58rem;color:#444;margin-top:0.2rem">Clears wins, losses, flip history for all users. Balance kept.</div></div>
            <div class="fw" style="justify-content:flex-end;padding-top:1.4rem"><button class="btn-ghost danger" onclick="masterResetAllStats()">Reset All Stats</button></div>
          </div>
          <div class="form-fb" id="dangerFb"></div>
        </div>
      </div>
    </div>

    <!-- Tab: Broadcast -->
    <div class="tab-pane" id="masterTab-broadcast">
      <div class="section-block">
        <span class="section-title">Broadcast Announcement</span>
        <div class="form-card">
          <div class="fw"><label class="field-label">Message</label><input class="adm-input" id="bcMessage" placeholder="Type announcement…"/></div>
          <div style="display:flex;gap:0.8rem;align-items:center;flex-wrap:wrap">
            <button class="btn-primary purple" onclick="masterBroadcast()">Send to All</button>
            <button class="btn-ghost danger" onclick="masterClearAnn()">Clear Announcement</button>
            <div class="form-fb" id="bcFb"></div>
          </div>
        </div>
      </div>
      <div class="section-block">
        <span class="section-title">Current Announcement</span>
        <div class="global-notice" id="currentAnnNotice"><span>No active announcement</span></div>
      </div>
    </div>

    <!-- Tab: Logs -->
    <div class="tab-pane" id="masterTab-logs">
      <div style="display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:0.8rem">
        <span class="section-title" style="flex:1">Full Platform Log</span>
        <button class="btn-ghost danger" onclick="clearLogs()">Clear Logs</button>
      </div>
      <div class="activity-log" style="max-height:500px" id="masterFullLog"><div style="font-size:0.6rem;color:#2a2a2a;padding:0.5rem">No activity yet</div></div>
    </div>
  </div>
</div>

<!-- BROKE -->
<div class="broke-overlay" id="brokeOverlay">
  <div class="broke-box">
    <h2>BROKE!</h2>
    <p>You've run out of funds.<br>Contact admin or wait for a top-up.</p>
    <button class="broke-close" onclick="document.getElementById('brokeOverlay').classList.remove('show')">Close</button>
  </div>
</div>

<script>
// ══════════════════════════════════════════════
// DATABASE
// ══════════════════════════════════════════════
const DEFAULT_START = 1000;

let DB = {
  users: [
    mk('u0','master','master123','master',10000),
    mk('u1','admin','admin123','admin',5000)
  ],
  announcement: '',
  globalMaxBet: 0,
  globalStartBal: DEFAULT_START,
  maintenance: false,
  signupsAllowed: true,
  activityLog: []
};

function mk(id,username,password,role,balance){
  return {id,username,password,role,balance,banned:false,walletLocked:false,gameLocked:false,maxBet:0,totalWon:0,totalLost:0,wins:0,flips:0,txHistory:[],history:[]};
}
function getUser(id){return DB.users.find(u=>u.id===id)}
function getUserByName(n){return DB.users.find(u=>u.username.toLowerCase()===n.toLowerCase())}
function saveUser(u){const i=DB.users.findIndex(x=>x.id===u.id);if(i>=0)DB.users[i]=u}
function UID(){return 'u'+Date.now()+Math.floor(Math.random()*999)}
function fmt(n){return(n<0?'-':'')+Math.abs(n).toLocaleString()}
function TS(){const n=new Date();return String(n.getHours()).padStart(2,'0')+':'+String(n.getMinutes()).padStart(2,'0')+':'+String(n.getSeconds()).padStart(2,'0')}

let currentUser = null;
let selectedAdminUID = null;
let selectedMasterUID = null;

// ── Log ──
function addLog(icon, msg, actor){
  const entry={time:TS(),icon,msg,actor:actor||currentUser?.username||'system'};
  DB.activityLog.unshift(entry);
  if(DB.activityLog.length>200) DB.activityLog.pop();
}
function renderLog(elId, limit=50){
  const el=document.getElementById(elId);
  if(!el) return;
  const entries=DB.activityLog.slice(0,limit);
  if(!entries.length){el.innerHTML='<div style="font-size:0.6rem;color:#2a2a2a;padding:0.5rem">No activity yet</div>';return}
  el.innerHTML=entries.map(e=>`<div class="log-row"><span class="log-time">${e.time}</span><span class="log-icon">${e.icon}</span><div class="log-msg">${e.msg} <strong style="color:#3a3a3a">[${e.actor}]</strong></div></div>`).join('');
}
function fb(id,msg,err=false,dur=2500){
  const el=document.getElementById(id);if(!el)return;
  el.textContent=msg;el.className=(id.startsWith('dp-')||id.startsWith('adp')||id.startsWith('mdp'))?'dp-fb'+(err?' err':''):'form-fb'+(err?' err':'');
  setTimeout(()=>{if(el)el.textContent=''},dur);
}

// ══════════════════════════════════════════════
// SCREENS
// ══════════════════════════════════════════════
function showScreen(id){document.querySelectorAll('.screen').forEach(s=>s.classList.remove('active'));document.getElementById(id).classList.add('active')}
function showGame(){showScreen('gameScreen');renderGame()}
function showAdmin(){if(!currentUser||(currentUser.role!=='admin'&&currentUser.role!=='master'))return;showScreen('adminScreen');renderAdminPanel()}
function showMaster(){if(!currentUser||currentUser.role!=='master')return;showScreen('masterScreen');renderMasterPanel()}

// ══════════════════════════════════════════════
// AUTH
// ══════════════════════════════════════════════
function switchTab(t){
  document.getElementById('tabSI').classList.toggle('active',t==='signin');
  document.getElementById('tabSU').classList.toggle('active',t==='signup');
  document.getElementById('formSI').style.display=t==='signin'?'flex':'none';
  document.getElementById('formSU').style.display=t==='signup'?'flex':'none';
}
function doSignIn(){
  const u=document.getElementById('siU').value.trim();
  const p=document.getElementById('siP').value;
  const found=getUserByName(u);
  if(!found){document.getElementById('siErr').textContent='User not found.';return}
  if(found.password!==p){document.getElementById('siErr').textContent='Wrong password.';return}
  if(found.banned){document.getElementById('siErr').textContent='Account banned.';return}
  if(DB.maintenance&&found.role==='user'){document.getElementById('siErr').textContent='Platform under maintenance.';return}
  document.getElementById('siErr').textContent='';
  addLog('🔑',`<strong>${found.username}</strong> signed in`,found.username);
  loginAs(found);
}
function doSignUp(){
  if(!DB.signupsAllowed){document.getElementById('suErr').textContent='Signups currently disabled.';return}
  const u=document.getElementById('suU').value.trim();
  const p=document.getElementById('suP').value;
  const p2=document.getElementById('suP2').value;
  if(!u||u.length<3){document.getElementById('suErr').textContent='Username 3+ chars.';return}
  if(getUserByName(u)){document.getElementById('suErr').textContent='Username taken.';return}
  if(p.length<6){document.getElementById('suErr').textContent='Password 6+ chars.';return}
  if(p!==p2){document.getElementById('suErr').textContent='Passwords do not match.';return}
  document.getElementById('suErr').textContent='';
  const nu=mk(UID(),u,p,'user',DB.globalStartBal||DEFAULT_START);
  DB.users.push(nu);
  addLog('👤',`New user <strong>${u}</strong> registered`,u);
  loginAs(nu);
}
function loginAs(user){
  currentUser=user;
  document.getElementById('topbar').classList.add('show');
  document.getElementById('topbarUser').textContent=user.username;
  const rb=document.getElementById('roleBadge');
  rb.textContent=user.role;rb.className='role-badge '+user.role;
  document.getElementById('btnNavAdmin').style.display=(user.role==='admin'||user.role==='master')?'inline-block':'none';
  document.getElementById('btnNavMaster').style.display=user.role==='master'?'inline-block':'none';
  showGame();
}
function logout(){
  if(currentUser)saveUser(currentUser);
  addLog('🚪',`<strong>${currentUser?.username}</strong> signed out`,currentUser?.username||'?');
  currentUser=null;
  document.getElementById('topbar').classList.remove('show');
  document.getElementById('siU').value='';document.getElementById('siP').value='';
  showScreen('authScreen');
}

// ══════════════════════════════════════════════
// GAME
// ══════════════════════════════════════════════
let gameState={bet:0,pick:null,flipping:false};

function renderGame(){
  if(!currentUser)return;
  const u=getUser(currentUser.id);currentUser=u;
  // announcement
  const ann=document.getElementById('annBanner');
  if(DB.announcement){document.getElementById('annText').textContent=DB.announcement;ann.classList.add('show')}
  else ann.classList.remove('show');
  // global max bet notice
  const notice=document.getElementById('walletNotice');
  if(DB.globalMaxBet>0){notice.textContent='⚠ Global max bet: $'+DB.globalMaxBet;notice.classList.add('show')}
  else if(u.maxBet>0){notice.textContent='⚠ Your max bet capped at $'+u.maxBet;notice.classList.add('show')}
  else notice.classList.remove('show');
  document.getElementById('walletRestricted').classList.toggle('show',u.walletLocked);
  document.getElementById('betSection').style.opacity=u.walletLocked||u.gameLocked?'0.3':'1';
  document.getElementById('betSection').style.pointerEvents=u.walletLocked||u.gameLocked?'none':'';
  updateWalletUI(0);refreshChipState();renderTx();renderHist();
}
function dismissAnnouncement(){document.getElementById('annBanner').classList.remove('show')}
function updateWalletUI(delta){
  const u=currentUser;
  const el=document.getElementById('walletAmt');
  el.textContent=fmt(u.balance);
  el.classList.remove('win-flash','lose-flash');void el.offsetWidth;
  if(delta>0)el.classList.add('win-flash');else if(delta<0)el.classList.add('lose-flash');
  setTimeout(()=>el.classList.remove('win-flash','lose-flash'),900);
  const d=document.getElementById('walletDelta');
  if(delta!==0){d.textContent=(delta>0?'+':'−')+'$'+Math.abs(delta).toLocaleString();d.className='wallet-delta show '+(delta>0?'pos':'neg');setTimeout(()=>{d.className='wallet-delta'},2000)}
  const pnl=u.totalWon-u.totalLost;
  document.getElementById('totalWon').textContent='$'+u.totalWon.toLocaleString();
  document.getElementById('totalLost').textContent='$'+u.totalLost.toLocaleString();
  const pe=document.getElementById('totalPnl');pe.textContent=(pnl>=0?'+':'')+'$'+fmt(pnl);pe.className='wstat-val '+(pnl>0?'g':pnl<0?'r':'n');
  const wr=u.flips>0?Math.round(u.wins/u.flips*100):null;
  document.getElementById('winRate').textContent=wr!==null?wr+'%':'—';
}
function effMaxBet(){
  const u=currentUser;
  const gm=DB.globalMaxBet||0;const um=u.maxBet||0;
  if(gm>0&&um>0)return Math.min(gm,um);
  return gm||um||0;
}
function selectChip(amount,btn){
  if(currentUser.walletLocked||currentUser.balance<amount)return;
  const cap=effMaxBet();if(cap>0&&amount>cap){alert('Max bet cap: $'+cap);return}
  gameState.bet=amount;document.querySelectorAll('.chip').forEach(c=>c.classList.remove('active'));btn.classList.add('active');document.getElementById('betInput').value='';tryEnableFlip();
}
function onBetInput(){
  const raw=parseInt(document.getElementById('betInput').value)||0;
  const cap=effMaxBet();let val=Math.max(0,Math.min(raw,currentUser.balance));
  if(cap>0)val=Math.min(val,cap);gameState.bet=val;document.querySelectorAll('.chip').forEach(c=>c.classList.remove('active'));tryEnableFlip();
}
function allIn(){
  if(currentUser.walletLocked||currentUser.balance<=0)return;
  let val=currentUser.balance;const cap=effMaxBet();if(cap>0)val=Math.min(val,cap);
  gameState.bet=val;document.getElementById('betInput').value=val;document.querySelectorAll('.chip').forEach(c=>c.classList.remove('active'));tryEnableFlip();
}
function refreshChipState(){
  const u=currentUser;const cap=effMaxBet();
  document.querySelectorAll('.chip').forEach(c=>{
    const v=parseInt(c.dataset.amt);const overCap=cap>0&&v>cap;
    c.classList.toggle('chip-disabled',u.balance<v||u.walletLocked||u.gameLocked||overCap);
  });
}
function tryEnableFlip(){
  const u=currentUser;const ready=gameState.pick&&gameState.bet>0&&gameState.bet<=u.balance&&!gameState.flipping&&!u.walletLocked&&!u.gameLocked;
  document.getElementById('flipBtn').disabled=!ready;
}
function pick(side){
  if(gameState.flipping||currentUser.walletLocked||currentUser.gameLocked)return;
  gameState.pick=side;
  document.getElementById('btnHeads').className='pick-btn'+(side==='heads'?' selected':'');
  document.getElementById('btnTails').className='pick-btn'+(side==='tails'?' selected':'');
  document.getElementById('pickLabel').textContent='You picked: '+side.toUpperCase();tryEnableFlip();
}
function flipCoin(){
  const u=currentUser;if(gameState.flipping||!gameState.pick||gameState.bet<=0||gameState.bet>u.balance||u.walletLocked||u.gameLocked)return;
  gameState.flipping=true;document.getElementById('flipBtn').disabled=true;
  const rt=document.getElementById('resultText');rt.className='result-text';
  const result=Math.random()<0.5?'heads':'tails';
  const coin=document.getElementById('coin');coin.className='coin flipping '+(result==='heads'?'heads-result':'tails-result');
  const betSnap=gameState.bet,pickSnap=gameState.pick;
  setTimeout(()=>{
    gameState.flipping=false;u.flips++;
    const won=pickSnap===result;const delta=won?betSnap:-betSnap;
    u.balance+=delta;if(won){u.totalWon+=betSnap;u.wins++}else{u.totalLost+=betSnap}
    saveUser(u);currentUser=u;updateWalletUI(delta);refreshChipState();
    rt.textContent=result.toUpperCase();rt.className='result-text visible '+(result==='heads'?'heads-r':'tails-r');
    const bH=document.getElementById('btnHeads'),bT=document.getElementById('btnTails');
    if(won){(pickSnap==='heads'?bH:bT).className='pick-btn correct';(pickSnap==='heads'?bT:bH).className='pick-btn'}
    else{(pickSnap==='heads'?bH:bT).className='pick-btn wrong';(pickSnap==='heads'?bT:bH).className='pick-btn correct'}
    document.getElementById('pickLabel').textContent=won?'🎯 Correct! +$'+betSnap:'✗ Wrong! −$'+betSnap;
    addTx(result,pickSnap,betSnap,won);addHistDot(result,won);
    addLog(won?'🟢':'🔴',`<strong>${u.username}</strong> flipped ${result} (bet $${betSnap}) — ${won?'WON':'LOST'}`,u.username);
    if(u.balance<=0)setTimeout(()=>document.getElementById('brokeOverlay').classList.add('show'),800);
    setTimeout(()=>{gameState.pick=null;gameState.bet=0;bH.className='pick-btn';bT.className='pick-btn';document.getElementById('pickLabel').textContent='— Choose your side —';document.getElementById('betInput').value='';document.querySelectorAll('.chip').forEach(c=>c.classList.remove('active'));document.getElementById('flipBtn').disabled=true;},1600);
  },1050);
}
function addTx(result,ps,bet,won){currentUser.txHistory.unshift({result,ps,bet,won,time:TS()});saveUser(currentUser);renderTx()}
function renderTx(){
  const log=document.getElementById('txLog');log.innerHTML='';
  if(!currentUser.txHistory.length){log.innerHTML='<div class="tx-empty">No bets yet</div>';return}
  currentUser.txHistory.forEach(tx=>{
    const row=document.createElement('div');row.className='tx-row';
    row.innerHTML='<div class="tx-face '+(tx.result==='heads'?'h':'t')+'">'+(tx.result==='heads'?'H':'T')+'</div>'+
      '<div class="tx-info"><strong>'+tx.result.toUpperCase()+'</strong> · bet '+tx.ps+'</div>'+
      '<div class="tx-amount '+(tx.won?'win':'lose')+'">'+(tx.won?'+':'−')+'$'+tx.bet+'</div>'+
      '<div class="tx-time">'+tx.time+'</div>';
    log.appendChild(row);requestAnimationFrame(()=>requestAnimationFrame(()=>row.classList.add('show')));
  });
}
function addHistDot(result,win){currentUser.history.unshift({result,win});if(currentUser.history.length>40)currentUser.history.pop();saveUser(currentUser);renderHist()}
function renderHist(){
  const c=document.getElementById('historyDots');c.innerHTML='';
  currentUser.history.forEach(item=>{
    const dot=document.createElement('div');dot.className='h-dot '+(item.result==='heads'?'h':'t')+(item.win?' win':'');
    c.appendChild(dot);requestAnimationFrame(()=>requestAnimationFrame(()=>dot.classList.add('show')));
  });
}

// ══════════════════════════════════════════════
// ADMIN PANEL
// ══════════════════════════════════════════════
function switchAdminTab(name,btn){
  document.querySelectorAll('#adminScreen .tab-btn').forEach(b=>b.classList.remove('active'));
  document.querySelectorAll('#adminScreen .tab-pane').forEach(p=>p.classList.remove('active'));
  btn.classList.add('active');document.getElementById('adminTab-'+name).classList.add('active');
  if(name==='activity')renderLog('adminActivityLog',100);
}
function renderAdminPanel(){
  renderAdminStats();renderAdminUsers();renderLog('adminActivityLog',100);
}
function renderAdminStats(){
  const users=DB.users.filter(u=>u.role==='user');
  const totalBal=users.reduce((s,u)=>s+u.balance,0);
  const totalFlips=users.reduce((s,u)=>s+u.flips,0);
  const banned=DB.users.filter(u=>u.banned).length;
  document.getElementById('adminStats').innerHTML=`
    <div class="stat-card"><span class="sc-label">Users</span><span class="sc-val y">${users.length}</span></div>
    <div class="stat-card"><span class="sc-label">Total Balance</span><span class="sc-val g">$${totalBal.toLocaleString()}</span></div>
    <div class="stat-card"><span class="sc-label">Total Flips</span><span class="sc-val w">${totalFlips}</span></div>
    <div class="stat-card"><span class="sc-label">Banned</span><span class="sc-val r">${banned}</span></div>
  `;
}
function renderAdminUsers(){
  const q=(document.getElementById('adminSearch')?.value||'').toLowerCase();
  const list=document.getElementById('adminUsersList');list.innerHTML='';
  const users=DB.users.filter(u=>u.username.toLowerCase().includes(q));
  if(!users.length){list.innerHTML='<div style="padding:0.8rem;font-size:0.62rem;color:#2a2a2a">No users found</div>';return}
  users.forEach(u=>{
    const isSelf=currentUser&&u.id===currentUser.id;
    const status=u.banned?'banned':u.walletLocked?'restricted':'active';
    const row=document.createElement('div');
    row.className='tbl-row'+(u.id===selectedAdminUID?' selected-row':'')+(u.banned?' banned-row':'');
    row.style.gridTemplateColumns='1fr 70px 90px 80px 80px 80px';
    row.innerHTML=`<span class="td name">${u.username}${isSelf?' ★':''}</span><span class="td role-${u.role}">${u.role}</span><span class="td bal">$${u.balance.toLocaleString()}</span><span class="td status-${status}">${status}</span><span class="td num">${u.maxBet>0?'$'+u.maxBet:'—'}</span><div class="td actions"><button class="act-btn" onclick="openAdminDetail('${u.id}')">Edit</button></div>`;
    list.appendChild(row);
  });
}
function openAdminDetail(uid){
  selectedAdminUID=uid;const u=getUser(uid);if(!u)return;
  document.getElementById('adpName').textContent=u.username;
  document.getElementById('adpMeta').textContent=`Role: ${u.role} · Balance: $${u.balance.toLocaleString()} · Flips: ${u.flips} · W/L: ${u.wins}/${u.flips-u.wins}`;
  document.getElementById('adpBal').value=u.balance;
  document.getElementById('adpMaxBet').value=u.maxBet||'';
  const wl=document.getElementById('adpWLock');wl.className='toggle'+(u.walletLocked?' on':'');
  document.getElementById('adpWLockLbl').textContent=u.walletLocked?'Locked':'Unlocked';
  document.getElementById('adpBanBtn').textContent=u.banned?'Unban':'Ban';
  document.getElementById('adpFb').textContent='';
  document.getElementById('adminDetailPanel').classList.add('open');
  document.getElementById('adminDetailPanel').scrollIntoView({behavior:'smooth',block:'nearest'});
  renderAdminUsers();
}
function closeAdminDetail(){selectedAdminUID=null;document.getElementById('adminDetailPanel').classList.remove('open');renderAdminUsers()}
function adminSetBal(){
  const u=getUser(selectedAdminUID);if(!u)return;
  const val=parseInt(document.getElementById('adpBal').value);
  if(isNaN(val)||val<0){fb('adpFb','Invalid amount',true);return}
  const old=u.balance;u.balance=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(0)}
  addLog('💰',`Admin set <strong>${u.username}</strong>'s balance to $${val} (was $${old})`);
  fb('adpFb','Balance → $'+val);renderAdminStats();renderAdminUsers();
}
function adminAddFunds(){
  const u=getUser(selectedAdminUID);if(!u)return;
  const val=parseInt(document.getElementById('adpAdd').value);
  if(isNaN(val)||val<=0){fb('adpFb','Enter valid amount',true);return}
  u.balance+=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(val)}
  addLog('➕',`Admin added $${val} to <strong>${u.username}</strong>`);
  fb('adpFb','+$'+val+' added');document.getElementById('adpBal').value=u.balance;renderAdminStats();renderAdminUsers();
}
function adminDeductFunds(){
  const u=getUser(selectedAdminUID);if(!u)return;
  const val=parseInt(document.getElementById('adpAdd').value);
  if(isNaN(val)||val<=0){fb('adpFb','Enter valid amount',true);return}
  u.balance=Math.max(0,u.balance-val);saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(-val)}
  addLog('➖',`Admin deducted $${val} from <strong>${u.username}</strong>`);
  fb('adpFb','−$'+val+' deducted');document.getElementById('adpBal').value=u.balance;renderAdminStats();renderAdminUsers();
}
function adminSetMaxBet(){
  const u=getUser(selectedAdminUID);if(!u)return;
  const val=parseInt(document.getElementById('adpMaxBet').value)||0;
  u.maxBet=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;refreshChipState()}
  addLog('🎰',`Admin set max bet for <strong>${u.username}</strong> → ${val>0?'$'+val:'unlimited'}`);
  fb('adpFb',val>0?'Max bet $'+val:'Restriction removed');renderAdminUsers();
}
function adminToggleWalletLock(){
  const u=getUser(selectedAdminUID);if(!u)return;
  u.walletLocked=!u.walletLocked;saveUser(u);
  const wl=document.getElementById('adpWLock');wl.className='toggle'+(u.walletLocked?' on':'');
  document.getElementById('adpWLockLbl').textContent=u.walletLocked?'Locked':'Unlocked';
  if(currentUser&&u.id===currentUser.id){currentUser=u;renderGame()}
  addLog('🔒',`Admin ${u.walletLocked?'locked':'unlocked'} wallet of <strong>${u.username}</strong>`);
  fb('adpFb','Wallet '+(u.walletLocked?'locked':'unlocked'));renderAdminUsers();
}
function adminToggleBan(){
  const u=getUser(selectedAdminUID);if(!u)return;
  if(currentUser&&u.id===currentUser.id){fb('adpFb','Cannot ban yourself',true);return}
  if(u.role==='master'){fb('adpFb','Cannot ban master',true);return}
  u.banned=!u.banned;saveUser(u);
  document.getElementById('adpBanBtn').textContent=u.banned?'Unban':'Ban';
  addLog('🚫',`Admin ${u.banned?'banned':'unbanned'} <strong>${u.username}</strong>`);
  fb('adpFb',u.banned?'User banned':'User unbanned');renderAdminStats();renderAdminUsers();
}
function adminDeleteUser(){
  const u=getUser(selectedAdminUID);if(!u)return;
  if(currentUser&&u.id===currentUser.id){fb('adpFb','Cannot delete yourself',true);return}
  if(u.role==='master'){fb('adpFb','Cannot delete master',true);return}
  if(!confirm('Delete "'+u.username+'"?'))return;
  addLog('🗑',`Admin deleted user <strong>${u.username}</strong>`);
  DB.users=DB.users.filter(x=>x.id!==selectedAdminUID);
  closeAdminDetail();renderAdminStats();renderAdminUsers();
}
function adminCreateUser(){
  const un=document.getElementById('cuUser').value.trim();const pw=document.getElementById('cuPass').value;
  const role=document.getElementById('cuRole').value;const bal=parseInt(document.getElementById('cuBal').value)||DEFAULT_START;
  if(!un||un.length<3){fb('cuFb','Username 3+ chars',true);return}
  if(getUserByName(un)){fb('cuFb','Username taken',true);return}
  if(pw.length<6){fb('cuFb','Password 6+ chars',true);return}
  const u=mk(UID(),un,pw,role,bal);DB.users.push(u);
  addLog('👤',`Admin created user <strong>${un}</strong> [${role}] $${bal}`);
  document.getElementById('cuUser').value='';document.getElementById('cuPass').value='';
  fb('cuFb','User "'+un+'" created!');renderAdminStats();renderAdminUsers();
}

// ══════════════════════════════════════════════
// MASTER PANEL
// ══════════════════════════════════════════════
function switchMasterTab(name,btn){
  document.querySelectorAll('#masterScreen .tab-btn').forEach(b=>{b.classList.remove('active');b.classList.remove('purple-tab')});
  document.querySelectorAll('#masterScreen .tab-pane').forEach(p=>p.classList.remove('active'));
  btn.classList.add('active','purple-tab');document.getElementById('masterTab-'+name).classList.add('active');
  if(name==='overview'){renderMasterOverview()}
  if(name==='users'){renderMasterUsers()}
  if(name==='admins'){renderMasterAdmins()}
  if(name==='logs'){renderLog('masterFullLog',200)}
}
function renderMasterPanel(){
  renderMasterStats();renderMasterOverview();syncGlobalSettingsUI();
  // sync current announcement
  const cn=document.getElementById('currentAnnNotice');
  cn.innerHTML=DB.announcement?`<span>${DB.announcement}</span>`:'<span>No active announcement</span>';
}
function renderMasterStats(){
  const allUsers=DB.users.filter(u=>u.role==='user');
  const allAdmins=DB.users.filter(u=>u.role==='admin');
  const totalBal=DB.users.reduce((s,u)=>s+u.balance,0);
  const totalFlips=DB.users.reduce((s,u)=>s+u.flips,0);
  const totalWon=DB.users.reduce((s,u)=>s+u.totalWon,0);
  const banned=DB.users.filter(u=>u.banned).length;
  document.getElementById('masterStats').innerHTML=`
    <div class="stat-card"><span class="sc-label">Total Users</span><span class="sc-val y">${allUsers.length}</span></div>
    <div class="stat-card"><span class="sc-label">Admins</span><span class="sc-val p">${allAdmins.length}</span></div>
    <div class="stat-card"><span class="sc-label">Platform Balance</span><span class="sc-val g">$${totalBal.toLocaleString()}</span></div>
    <div class="stat-card"><span class="sc-label">Total Flips</span><span class="sc-val w">${totalFlips}</span></div>
    <div class="stat-card"><span class="sc-label">Total Wagered</span><span class="sc-val b">$${(totalWon*2).toLocaleString()}</span></div>
    <div class="stat-card"><span class="sc-label">Banned</span><span class="sc-val r">${banned}</span></div>
  `;
}
function renderMasterOverview(){
  // health grid
  const totalFlips=DB.users.reduce((s,u)=>s+u.flips,0);
  const avgBal=DB.users.length?Math.round(DB.users.reduce((s,u)=>s+u.balance,0)/DB.users.length):0;
  const activeToday=DB.users.filter(u=>u.flips>0).length;
  document.getElementById('masterHealthGrid').innerHTML=`
    <div class="stat-card"><span class="sc-label">Avg Balance</span><span class="sc-val g">$${avgBal.toLocaleString()}</span><span class="sc-sub">across all users</span></div>
    <div class="stat-card"><span class="sc-label">Active Players</span><span class="sc-val y">${activeToday}</span><span class="sc-sub">have played at least once</span></div>
    <div class="stat-card"><span class="sc-label">Total Flips</span><span class="sc-val w">${totalFlips}</span></div>
    <div class="stat-card"><span class="sc-label">Maintenance</span><span class="sc-val ${DB.maintenance?'r':'g'}">${DB.maintenance?'ON':'OFF'}</span></div>
    <div class="stat-card"><span class="sc-label">Signups</span><span class="sc-val ${DB.signupsAllowed?'g':'r'}">${DB.signupsAllowed?'OPEN':'CLOSED'}</span></div>
    <div class="stat-card"><span class="sc-label">Global Max Bet</span><span class="sc-val b">${DB.globalMaxBet>0?'$'+DB.globalMaxBet:'—'}</span></div>
  `;
  // leaderboard
  const sorted=[...DB.users].sort((a,b)=>b.balance-a.balance).slice(0,10);
  const lb=document.getElementById('masterLeaderboard');lb.innerHTML='';
  sorted.forEach((u,i)=>{
    const row=document.createElement('div');row.className='tbl-row';
    row.style.gridTemplateColumns='30px 1fr 100px 80px 80px';
    row.innerHTML=`<span class="td num">${i+1}</span><span class="td name">${u.username} <span class="role-${u.role}" style="font-size:0.5rem">${u.role}</span></span><span class="td bal ${u.balance>DEFAULT_START?'g':u.balance<DEFAULT_START?'r':''}">$${u.balance.toLocaleString()}</span><span class="td num">${u.wins}</span><span class="td num">${u.flips}</span>`;
    lb.appendChild(row);
  });
  renderLog('masterRecentLog',20);
}
function renderMasterUsers(){
  const q=(document.getElementById('masterSearch')?.value||'').toLowerCase();
  const list=document.getElementById('masterUsersList');list.innerHTML='';
  const users=DB.users.filter(u=>u.username.toLowerCase().includes(q));
  if(!users.length){list.innerHTML='<div style="padding:0.8rem;font-size:0.62rem;color:#2a2a2a">No users</div>';return}
  users.forEach(u=>{
    const isSelf=currentUser&&u.id===currentUser.id;
    const status=u.banned?'banned':u.walletLocked||u.gameLocked?'restricted':'active';
    const row=document.createElement('div');
    row.className='tbl-row'+(u.id===selectedMasterUID?' selected-row':'')+(u.banned?' banned-row':'');
    row.style.gridTemplateColumns='1fr 70px 90px 80px 70px 80px';
    row.innerHTML=`<span class="td name">${u.username}${isSelf?' ★':''}</span><span class="td role-${u.role}">${u.role}</span><span class="td bal">$${u.balance.toLocaleString()}</span><span class="td status-${status}">${status}</span><span class="td num">${u.flips}</span><div class="td actions"><button class="act-btn" onclick="openMasterDetail('${u.id}')">Edit</button></div>`;
    list.appendChild(row);
  });
}
function openMasterDetail(uid){
  selectedMasterUID=uid;const u=getUser(uid);if(!u)return;
  document.getElementById('mdpName').textContent=u.username;
  document.getElementById('mdpMeta').textContent=`Role: ${u.role} · Balance: $${u.balance.toLocaleString()} · Flips: ${u.flips} · W/L: ${u.wins}/${u.flips-u.wins} · Won: $${u.totalWon} · Lost: $${u.totalLost}`;
  document.getElementById('mdpBal').value=u.balance;
  document.getElementById('mdpMaxBet').value=u.maxBet||'';
  document.getElementById('mdpRole').value=u.role;
  const wl=document.getElementById('mdpWLock');wl.className='toggle'+(u.walletLocked?' on':'');
  document.getElementById('mdpWLockLbl').textContent=u.walletLocked?'Locked':'Unlocked';
  const gl=document.getElementById('mdpGameLock');gl.className='toggle'+(u.gameLocked?' on':'');
  document.getElementById('mdpGameLockLbl').textContent=u.gameLocked?'Blocked':'Allowed';
  document.getElementById('mdpBanBtn').textContent=u.banned?'Unban':'Ban';
  document.getElementById('mdpFb').textContent='';
  document.getElementById('masterDetailPanel').classList.add('open');
  document.getElementById('masterDetailPanel').scrollIntoView({behavior:'smooth',block:'nearest'});
  renderMasterUsers();
}
function closeMasterDetail(){selectedMasterUID=null;document.getElementById('masterDetailPanel').classList.remove('open');renderMasterUsers()}
function masterSetBal(){
  const u=getUser(selectedMasterUID);if(!u)return;
  const val=parseInt(document.getElementById('mdpBal').value);if(isNaN(val)||val<0){fb('mdpFb','Invalid',true);return}
  const old=u.balance;u.balance=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(0)}
  addLog('💰',`Master set <strong>${u.username}</strong> balance $${old}→$${val}`);
  fb('mdpFb','Balance → $'+val);renderMasterStats();renderMasterUsers();
}
function masterAddFunds(){
  const u=getUser(selectedMasterUID);if(!u)return;
  const val=parseInt(document.getElementById('mdpAdd').value);if(isNaN(val)||val<=0){fb('mdpFb','Enter amount',true);return}
  u.balance+=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(val)}
  addLog('➕',`Master added $${val} to <strong>${u.username}</strong>`);
  fb('mdpFb','+$'+val);document.getElementById('mdpBal').value=u.balance;renderMasterStats();renderMasterUsers();
}
function masterDeductFunds(){
  const u=getUser(selectedMasterUID);if(!u)return;
  const val=parseInt(document.getElementById('mdpAdd').value);if(isNaN(val)||val<=0){fb('mdpFb','Enter amount',true);return}
  u.balance=Math.max(0,u.balance-val);saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;updateWalletUI(-val)}
  addLog('➖',`Master deducted $${val} from <strong>${u.username}</strong>`);
  fb('mdpFb','−$'+val);document.getElementById('mdpBal').value=u.balance;renderMasterStats();renderMasterUsers();
}
function masterSetMaxBet(){
  const u=getUser(selectedMasterUID);if(!u)return;
  const val=parseInt(document.getElementById('mdpMaxBet').value)||0;u.maxBet=val;saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;refreshChipState()}
  addLog('🎰',`Master set max bet for <strong>${u.username}</strong> → ${val>0?'$'+val:'unlimited'}`);
  fb('mdpFb',val>0?'Max bet $'+val:'Limit removed');renderMasterUsers();
}
function masterSetRole(){
  const u=getUser(selectedMasterUID);if(!u)return;
  if(currentUser&&u.id===currentUser.id){fb('mdpFb','Cannot change own role',true);return}
  const newRole=document.getElementById('mdpRole').value;
  const oldRole=u.role;u.role=newRole;saveUser(u);
  addLog('🎖',`Master changed <strong>${u.username}</strong> role: ${oldRole}→${newRole}`);
  fb('mdpFb','Role → '+newRole);renderMasterStats();renderMasterUsers();renderMasterAdmins();
}
function masterToggleWalletLock(){
  const u=getUser(selectedMasterUID);if(!u)return;
  u.walletLocked=!u.walletLocked;saveUser(u);
  const wl=document.getElementById('mdpWLock');wl.className='toggle'+(u.walletLocked?' on':'');
  document.getElementById('mdpWLockLbl').textContent=u.walletLocked?'Locked':'Unlocked';
  if(currentUser&&u.id===currentUser.id){currentUser=u;renderGame()}
  addLog('🔒',`Master ${u.walletLocked?'locked':'unlocked'} wallet of <strong>${u.username}</strong>`);
  fb('mdpFb','Wallet '+(u.walletLocked?'locked':'unlocked'));renderMasterUsers();
}
function masterToggleGameLock(){
  const u=getUser(selectedMasterUID);if(!u)return;
  u.gameLocked=!u.gameLocked;saveUser(u);
  const gl=document.getElementById('mdpGameLock');gl.className='toggle'+(u.gameLocked?' on':'');
  document.getElementById('mdpGameLockLbl').textContent=u.gameLocked?'Blocked':'Allowed';
  if(currentUser&&u.id===currentUser.id){currentUser=u;renderGame()}
  addLog('🎮',`Master ${u.gameLocked?'blocked':'unblocked'} game access for <strong>${u.username}</strong>`);
  fb('mdpFb','Game access '+(u.gameLocked?'blocked':'allowed'));renderMasterUsers();
}
function masterToggleBan(){
  const u=getUser(selectedMasterUID);if(!u)return;
  if(currentUser&&u.id===currentUser.id){fb('mdpFb','Cannot ban yourself',true);return}
  u.banned=!u.banned;saveUser(u);
  document.getElementById('mdpBanBtn').textContent=u.banned?'Unban':'Ban';
  addLog('🚫',`Master ${u.banned?'banned':'unbanned'} <strong>${u.username}</strong>`);
  fb('mdpFb',u.banned?'Banned':'Unbanned');renderMasterStats();renderMasterUsers();
}
function masterResetStats(){
  const u=getUser(selectedMasterUID);if(!u)return;
  u.totalWon=0;u.totalLost=0;u.wins=0;u.flips=0;u.txHistory=[];u.history=[];saveUser(u);
  if(currentUser&&u.id===currentUser.id){currentUser=u;renderGame()}
  addLog('🔄',`Master reset stats for <strong>${u.username}</strong>`);
  fb('mdpFb','Stats reset');renderMasterUsers();
}
function masterDeleteUser(){
  const u=getUser(selectedMasterUID);if(!u)return;
  if(currentUser&&u.id===currentUser.id){fb('mdpFb','Cannot delete yourself',true);return}
  if(!confirm('Delete "'+u.username+'"? Cannot be undone.'))return;
  addLog('🗑',`Master deleted user <strong>${u.username}</strong>`);
  DB.users=DB.users.filter(x=>x.id!==selectedMasterUID);
  closeMasterDetail();renderMasterStats();renderMasterUsers();renderMasterAdmins();
}
function renderMasterAdmins(){
  const admins=DB.users.filter(u=>u.role==='admin');
  const list=document.getElementById('masterAdminsList');list.innerHTML='';
  if(!admins.length){list.innerHTML='<div style="padding:0.8rem;font-size:0.62rem;color:#2a2a2a">No admins</div>';return}
  admins.forEach(u=>{
    const row=document.createElement('div');row.className='tbl-row';row.style.gridTemplateColumns='1fr 90px 80px 80px 80px';
    const status=u.banned?'banned':u.walletLocked?'restricted':'active';
    row.innerHTML=`<span class="td name">${u.username}</span><span class="td bal">$${u.balance.toLocaleString()}</span><span class="td status-${status}">${status}</span><span class="td num">${u.flips}</span><div class="td actions"><button class="act-btn" onclick="switchMasterTab('users',document.querySelector('#masterScreen .tab-btn:nth-child(2)'));openMasterDetail('${u.id}')">Edit</button></div>`;
    list.appendChild(row);
  });
}
function masterCreateAdmin(){
  const un=document.getElementById('maUser').value.trim();const pw=document.getElementById('maPass').value;
  const bal=parseInt(document.getElementById('maBal').value)||5000;
  if(!un||un.length<3){fb('maFb','Username 3+ chars',true);return}
  if(getUserByName(un)){fb('maFb','Username taken',true);return}
  if(pw.length<6){fb('maFb','Password 6+ chars',true);return}
  const u=mk(UID(),un,pw,'admin',bal);DB.users.push(u);
  addLog('🎖',`Master created admin <strong>${un}</strong> $${bal}`);
  document.getElementById('maUser').value='';document.getElementById('maPass').value='';
  fb('maFb','Admin "'+un+'" created!');renderMasterStats();renderMasterAdmins();renderMasterUsers();
}

// Global Settings
function syncGlobalSettingsUI(){
  document.getElementById('gsMaxBet').value=DB.globalMaxBet||'';
  document.getElementById('gsStartBal').value=DB.globalStartBal||DEFAULT_START;
  const mt=document.getElementById('gsMaintToggle');mt.className='toggle'+(DB.maintenance?' on':'');
  document.getElementById('gsMaintLbl').textContent=DB.maintenance?'ON — users locked out':'Off';
  const sg=document.getElementById('gsSignupToggle');sg.className='toggle'+(DB.signupsAllowed?' on':'');
  document.getElementById('gsSignupLbl').textContent=DB.signupsAllowed?'Allowed':'Disabled';
}
function toggleMaintenance(){
  DB.maintenance=!DB.maintenance;
  addLog('🔧',`Master turned maintenance mode ${DB.maintenance?'ON':'OFF'}`);
  syncGlobalSettingsUI();renderMasterOverview();
}
function toggleSignups(){
  DB.signupsAllowed=!DB.signupsAllowed;
  addLog('📋',`Master ${DB.signupsAllowed?'enabled':'disabled'} new signups`);
  syncGlobalSettingsUI();
}
function applyGlobalSettings(){
  const mb=parseInt(document.getElementById('gsMaxBet').value)||0;
  const sb=parseInt(document.getElementById('gsStartBal').value)||DEFAULT_START;
  DB.globalMaxBet=mb;DB.globalStartBal=sb;
  addLog('⚙',`Master applied global settings: maxBet=${mb>0?'$'+mb:'unlimited'}, startBal=$${sb}`);
  if(currentUser)refreshChipState();
  fb('gsFb','Settings applied!');renderMasterStats();renderMasterOverview();
}
function masterBulkRefill(){
  const amt=parseInt(document.getElementById('bulkRefill').value);
  if(isNaN(amt)||amt<=0){fb('bulkFb','Enter valid amount',true);return}
  let count=0;
  DB.users.forEach(u=>{if(u.role==='user'){u.balance+=amt;saveUser(u);count++}});
  if(currentUser&&currentUser.role==='user'){currentUser=getUser(currentUser.id);updateWalletUI(amt)}
  addLog('💸',`Master bulk-refilled $${amt} to ${count} users`);
  fb('bulkFb',`$${amt} added to ${count} users`);renderMasterStats();
}
function masterLockAll(){
  if(!confirm('Lock wallets for ALL users?'))return;
  DB.users.forEach(u=>{if(u.role==='user'){u.walletLocked=true;saveUser(u)}});
  if(currentUser&&currentUser.role==='user'){currentUser=getUser(currentUser.id);renderGame()}
  addLog('🔒','Master locked ALL user wallets');fb('bulkFb','All user wallets locked');renderMasterUsers();
}
function masterUnlockAll(){
  DB.users.forEach(u=>{u.walletLocked=false;saveUser(u)});
  if(currentUser){currentUser=getUser(currentUser.id);renderGame()}
  addLog('🔓','Master unlocked ALL wallets');fb('bulkFb','All wallets unlocked');renderMasterUsers();
}
function masterResetAllStats(){
  if(!confirm('Reset ALL user stats? This cannot be undone.'))return;
  DB.users.forEach(u=>{u.totalWon=0;u.totalLost=0;u.wins=0;u.flips=0;u.txHistory=[];u.history=[];saveUser(u)});
  if(currentUser){currentUser=getUser(currentUser.id);renderGame()}
  addLog('🔄','Master reset ALL user stats platform-wide');fb('dangerFb','All stats reset');renderMasterStats();
}

// Broadcast
function masterBroadcast(){
  const msg=document.getElementById('bcMessage').value.trim();
  if(!msg){fb('bcFb','Enter a message',true);return}
  DB.announcement=msg;
  document.getElementById('currentAnnNotice').innerHTML=`<span>${msg}</span>`;
  if(document.getElementById('gameScreen').classList.contains('active')){
    document.getElementById('annText').textContent=msg;
    document.getElementById('annBanner').classList.add('show');
  }
  addLog('📢',`Master broadcast: "${msg}"`);fb('bcFb','Announcement sent!');document.getElementById('bcMessage').value='';
}
function masterClearAnn(){
  DB.announcement='';document.getElementById('currentAnnNotice').innerHTML='<span>No active announcement</span>';
  document.getElementById('annBanner').classList.remove('show');
  addLog('🔕','Master cleared announcement');fb('bcFb','Announcement cleared');
}
function clearLogs(){if(!confirm('Clear all logs?'))return;DB.activityLog=[];renderLog('masterFullLog',200);renderLog('adminActivityLog',100);renderLog('masterRecentLog',20)}

// Keyboard enter
document.addEventListener('keydown',e=>{
  if(e.key==='Enter'&&document.getElementById('authScreen').classList.contains('active')){
    document.getElementById('tabSI').classList.contains('active')?doSignIn():doSignUp();
  }
});
</script>
</body>
</html>
