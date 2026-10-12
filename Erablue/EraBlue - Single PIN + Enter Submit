// ==UserScript==
// @name         EraBlue - Single PIN + Enter Submit
// @namespace    http://tampermonkey.net/
// @version      1.1
// @description  Mengubah 6 input PIN menjadi satu input dan Enter untuk submit
// @match        https://auth.erablue.id/*
// @grant        none
// @run-at       document-idle
// ==/UserScript==

(function () {
    'use strict';

    function setupPIN() {
        const container = document.getElementById('divOptPassword');
        if (!container) return;

        const oldInputs = Array.from(
            container.querySelectorAll('input.digitnumberitem')
        );

        if (oldInputs.length !== 6) return;
        if (document.getElementById('tmSinglePIN')) return;

        // Sembunyikan input asli
        oldInputs.forEach(input => {
            input.style.display = 'none';
        });

        // Buat input baru
        const input = document.createElement('input');

        input.id = 'tmSinglePIN';
        input.type = 'tel';
        input.inputMode = 'numeric';
        input.maxLength = 6;
        input.autocomplete = 'one-time-code';
        input.className = 'form-control';
        input.placeholder = '******';

        input.style.cssText = `
            width: 100%;
            height: 40px;
            margin: 0;
            text-align: center;
            font-weight: bold;
            font-size: 18px;
            letter-spacing: 8px;
        `;

        container.insertBefore(input, oldInputs[0]);

        function updateOriginalInputs(value) {

            value = value
                .replace(/\D/g, '')
                .substring(0, 6);

            input.value = value;

            oldInputs.forEach((oldInput, index) => {

                oldInput.value = value[index] || '';

                oldInput.dispatchEvent(
                    new Event('input', {
                        bubbles: true
                    })
                );

                oldInput.dispatchEvent(
                    new Event('change', {
                        bubbles: true
                    })
                );

            });
        }

        // Input PIN
        input.addEventListener('input', function () {
            updateOriginalInputs(this.value);
        });

        // Paste PIN
        input.addEventListener('paste', function (e) {

            e.preventDefault();

            const text = (
                e.clipboardData ||
                window.clipboardData
            ).getData('text');

            updateOriginalInputs(text);
        });

        // Keyboard
        input.addEventListener('keydown', function (e) {

            // ENTER = KIRIM
            if (e.key === 'Enter') {

                e.preventDefault();
                e.stopPropagation();

                const pin = input.value.replace(/\D/g, '');

                // Harus 6 digit
                if (pin.length !== 6) {
                    return;
                }

                submitForm();
                return;
            }

            // Izinkan tombol kontrol
            if (
                e.key === 'Backspace' ||
                e.key === 'Delete' ||
                e.key === 'ArrowLeft' ||
                e.key === 'ArrowRight' ||
                e.key === 'Tab'
            ) {
                return;
            }

            // Hanya angka
            if (!/^[0-9]$/.test(e.key)) {
                e.preventDefault();
            }
        });

        input.focus();
    }

    function submitForm() {

        /*
         * Cari form tempat input PIN berada
         */
        const input = document.getElementById('tmSinglePIN');

        if (!input) return;

        const form = input.closest('form');

        if (form) {
            form.requestSubmit();
            return;
        }

        /*
         * Jika tidak menggunakan <form>,
         * cari tombol submit di sekitar PIN.
         */
        const container = document.getElementById('divOptPassword');

        if (!container) return;

        const button =
            container.querySelector(
                'button[type="submit"], input[type="submit"]'
            ) ||
            document.querySelector(
                'button[type="submit"], input[type="submit"]'
            );

        if (button) {
            button.click();
        }
    }

    setupPIN();

    // Antisipasi halaman membuat elemen secara dinamis
    const observer = new MutationObserver(() => {
        setupPIN();
    });

    observer.observe(document.documentElement, {
        childList: true,
        subtree: true
    });

})();
