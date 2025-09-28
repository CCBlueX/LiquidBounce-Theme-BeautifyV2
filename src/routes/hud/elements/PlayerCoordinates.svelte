<!--
  - This file is part of LiquidBounce (https://github.com/CCBlueX/LiquidBounce)
  -
  - Copyright (c) 2015 - 2025 CCBlueX
  -
  - LiquidBounce is free software: you can redistribute it and/or modify
  - it under the terms of the GNU General Public License as published by
  - the Free Software Foundation, either version 3 of the License, or
  - (at your option) any later version.
  -
  - LiquidBounce is distributed in the hope that it will be useful,
  - but WITHOUT ANY WARRANTY; without even the implied warranty of
  - MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the
  - GNU General Public License for more details.
  -
  - You should have received a copy of the GNU General Public License
  - along with LiquidBounce. If not, see <https://www.gnu.org/licenses/>.
  -->

<script lang="ts">
    import { listen } from "../../../integration/ws";
    import type { ClientPlayerDataEvent } from "../../../integration/events";
    import { fly } from "svelte/transition";

    let x = 0, y = 0, z = 0;

    listen("clientPlayerData", (event: ClientPlayerDataEvent) => {
        x = event.playerData.position.x;
        y = event.playerData.position.y;
        z = event.playerData.position.z;
    });
</script>

<div class="coordinates-container" transition:fly|local={{ x: -40, duration: 300 }}>
    <div class="icon-wrapper">
        <svg class="coordinates-icon" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 640 640">
            <path fill="#fff" d="M119.7 263.7L150.6 294.6C156.6 300.6 164.7 304 173.2 304L194.7 304C203.2 304 211.3 307.4 217.3 313.4L246.6 342.7C252.6 348.7 256 356.8 256 365.3L256 402.8C256 411.3 259.4 419.4 265.4 425.4L278.7 438.7C284.7 444.7 288.1 452.8 288.1 461.3L288.1 480C288.1 497.7 302.4 512 320.1 512C337.8 512 352.1 497.7 352.1 480L352.1 477.3C352.1 468.8 355.5 460.7 361.5 454.7L406.8 409.4C412.8 403.4 416.2 395.3 416.2 386.8L416.2 352.1C416.2 334.4 401.9 320.1 384.2 320.1L301.5 320.1C293 320.1 284.9 316.7 278.9 310.7L262.9 294.7C258.7 290.5 256.3 284.7 256.3 278.7C256.3 266.2 266.4 256.1 278.9 256.1L313.6 256.1C326.1 256.1 336.2 246 336.2 233.5C336.2 227.5 333.8 221.7 329.6 217.5L309.9 197.8C306 194 304 189.1 304 184C304 178.9 306 174 309.7 170.3L327 153C332.8 147.2 336.1 139.3 336.1 131.1C336.1 123.9 333.7 117.4 329.7 112.2C326.5 112.1 323.3 112 320.1 112C224.7 112 144.4 176.2 119.8 263.7zM528 320C528 285.4 519.6 252.8 504.6 224.2C498.2 225.1 491.9 228.1 486.7 233.3L473.3 246.7C467.3 252.7 463.9 260.8 463.9 269.3L463.9 304C463.9 321.7 478.2 336 495.9 336L520 336C522.5 336 525 335.7 527.3 335.2C527.7 330.2 527.8 325.1 527.8 320zM64 320C64 178.6 178.6 64 320 64C461.4 64 576 178.6 576 320C576 461.4 461.4 576 320 576C178.6 576 64 461.4 64 320z"/>
        </svg>
    </div>
    <div class="coordinates-text">
        <span>X: {x.toFixed(1)}</span>
        <span>Y: {y.toFixed(1)}</span>
        <span>Z: {z.toFixed(1)}</span>
    </div>
</div>

<style lang="scss">
  @use "../../../colors.scss" as *;

  .coordinates-container {
    display: flex;
    align-items: center;
    gap: 10px;
    font-family: MyCustomFont;
    color: white;
    font-size: 14px;
    font-weight: 500;
    background: rgba($playercoords-base-color, 0.55);
    border: 1px solid rgba(150, 150, 150, 0.15);
    border-radius: 7.5px;
    padding: 5px 15px;
    min-width: 220px;
    box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
  }

  .icon-wrapper {
    flex-shrink: 0;
  }

  .coordinates-icon {
    width: 20px;
    height: 20px;
    vertical-align: middle;
    opacity: 1;
  }

  .coordinates-text {
    display: flex;
    gap: 12px;
    flex: 1;
  }
</style>
