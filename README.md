from starkware.starknet.business_logic.contract_definition import StarknetContract
from starkware.starknet.definitions import starknet

# Stefcio magik programowania

@starknet.contract
def DexContract():
    # Struktura reprezentująca pojedynczą transakcję na rynku.
    transaction: record(
        trader: address,
        amount: uint256,
        price: uint256,
        buy: bool,
      parrarararra
xd
    # Struktura reprezentująca pojedyncą pozycję na rynku.
    position: record(
        trader: address,
        amount: uint256,
        price: uint256,
    )

    # Lista transakcji na rynku.
    transactions: public(transaction[10])

    # Lista otwartych pozycji na rynku.
    open_positions: public(position[10])

    # Funkcja do wykonania transakcji.
    @public
    def trade(
        amount: uint256,
        price: uint256,
        buy: bool,
    ):
        # Sprawdzenie, czy jest wystarczająca ilość środków w koncie tradera.
        assert self.starknet_balance >= amount * price, "Insufficient funds."

        # Dodanie nowej transakcji do listy.
        self.transactions[0] = self.transaction(
            trader=msg.sender,
            amount=amount,
            price=price,
            buy=buy,
        )

        # Zaktualizowanie pozycji na rynku.
        self.update_position(amount, price, buy)

    # Funkcja do aktualizacji pozycji na rynku.
    @public
    def update_position(
        amount: uint256,
        price: uint256,
        buy: bool,
    ):
        # Sprawdzenie, czy pozycja już istnieje.
        existing_position_index: uint256 = 0
        position_exists: bool = False
        for i in range(10):
            if self.open_positions[i].trader == msg.sender:
                existing_position_index = i
                position_exists = True
                break

        # Aktualizacja pozycji w zależności od tego, czy istnieje.
        if position_exists:
            if buy:
                # Aktualizacja istniejącej pozycji po zakupie.
                self.open_positions[existing_position_index].amount += amount
                self.open_positions[existing_position_index].price = (
                    self.open_positions[existing_position_index].price + price
                ) / 2
            else:
                # Aktualizacja istniejącej pozycji po sprzedaży.
                self.open_positions[existing_position_index].amount -= amount
                if self.open_positions[existing_position_index].amount == 0:
                    # Usunięcie pozycji, jeśli jej ilość spadnie do zera.
                    self.open_positions[existing_position_index] = self.position(
                        trader=0, amount=0, price=0
                    )
        else:
            # Dodanie nowej pozycji na rynku, jeśli nie istnieje.
            for i in range(10):
                if self.open_positions[i].trader == 0:
                    self.open_positions[i] = self.position(
                        trader=msg.sender, amount=amount, price=price
                    )
                    break

    # Funkcja do pobrania otwartych pozycji na rynku.
    @public
    @view
    def get_open_positions() -> position[10]:
        return self.open_positions
