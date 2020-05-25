// ==UserScript==
// @name         LSS-Mission-Alarm-Speaker Beta2
// @version      1.0.0
// @description  Speaks alarmed vehicles out
// @author       Jonas (HiOrg-Jonas)
// @include      /https:\/\/www.leitstellenspiel.de/missions/\d+/
// @grant        none
// ==/UserScript==
(function() {
    'use strict';
    const alarmBtns = document.getElementById('mission_alarm_btn').parentElement.children;
    Array.from(alarmBtns).forEach(btn => btn.addEventListener('click', () => {
        const vehicles = Array.from(document.querySelectorAll('#vehicle_show_table_body_all .vehicle_select_table_tr')).filter(r => r.querySelector('.vehicle_checkbox:checked'));
        const buildings = {};
        vehicles.forEach(vehicle => {
            const building = vehicle.querySelector('a[href^="/buildings/"]');
            if (!building) return;
            const buildingCaption = building.textContent.trim();
            const buildingId = building.href.match(/\d+$/)[0];
            if (!buildings.hasOwnProperty(buildingId)) buildings[buildingId] = {caption: buildingCaption, vehicles: []};
            buildings[buildingId].vehicles.push(vehicle.querySelector('.mission_vehicle_label').textContent.trim());
        });
        let speech = 'Alarm für: ' + Object.values(buildings).map(b => `Von der Wache ${b.caption}: ${b.vehicles}!`).join(' ') + 'Für: ' + document.getElementById('missionH1').textContent.trim();
        console.log(speech);
        // Object.keys(replacements).forEach(rep => speech = speech.replace(new RegExp(rep.replace(/(^\/)|(\/$)/g, '')), replacements[rep]));
        // speech = speech.replace(/[\u{1f300}-\u{1f5ff}\u{1f900}-\u{1f9ff}\u{1f600}-\u{1f64f}\u{1f680}-\u{1f6ff}\u{2600}-\u{26ff}\u{2700}-\u{27bf}\u{1f1e6}-\u{1f1ff}\u{1f191}-\u{1f251}\u{1f004}\u{1f0cf}\u{1f170}-\u{1f171}\u{1f17e}-\u{1f17f}\u{1f18e}\u{3030}\u{2b50}\u{2b55}\u{2934}-\u{2935}\u{2b05}-\u{2b07}\u{2b1b}-\u{2b1c}\u{3297}\u{3299}\u{303d}\u{00a9}\u{00ae}\u{2122}\u{23f3}\u{24c2}\u{23e9}-\u{23ef}\u{25b6}\u{23f8}-\u{23fa}\u{200d}]/ug, '');
        speech = speech.replace(/ +/g, ' ');
        console.log(speech);
        tellParent(`const alarmt2s = new SpeechSynthesisUtterance();alarmt2s.text = ${JSON.stringify(speech)};alarmt2s.lang = speechSynthesis.getVoices().find(voice => voice.lang === 'de');alarmt2s.rate = 1;speechSynthesis.speak(alarmt2s)`);
    }));
})();
