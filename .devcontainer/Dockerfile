FROM alpine:3.23

# Install necessary packages for development.
RUN apk update && apk add --no-cache git gnupg openssh curl zsh

# Create a non-root user for development
RUN adduser -D -s /bin/zsh dev
USER dev
WORKDIR /home/dev

# Install oh-my-zsh for the dev user
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended \
	&& echo 'export PATH="${BUN_INSTALL}/bin:${PATH}"' >> /home/dev/.zshrc || true